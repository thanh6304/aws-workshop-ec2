---
title: "5.5.5 Process Messages from Amazon SQS"
date: 2024-01-01
weight: 5
chapter: false
---


## Overview

Amazon SQS decouples the Backend Lambda from the AI Worker Lambda by enabling asynchronous request processing.

When a user submits an AI prediction request, the Backend Lambda publishes a message to Amazon SQS. The AI Worker Lambda consumes the message, queries Amazon Athena, invokes Amazon Bedrock, and stores the result in Amazon RDS PostgreSQL.

This design improves responsiveness, scalability, reliability, and fault tolerance.

---

## Architecture

```text
API Gateway
      │
      ▼
Backend Lambda
      │
      ▼
Amazon SQS
      │
      ▼
AI Worker Lambda
      │
      ├── Amazon Athena
      ├── Amazon Bedrock
      └── Amazon RDS PostgreSQL
```

---

## Objectives

After completing this section, you will:

- Define the SQS message structure.
- Process SQS events in Lambda.
- Configure batch processing.
- Configure Visibility Timeout.
- Configure retries and a Dead Letter Queue.
- Enable Partial Batch Response.
- Implement idempotent message handling.

---

## Example Message

```json
{
  "requestId": "REQ-20260722-001",
  "requestType": "DEMAND_FORECAST",
  "warehouse": "WH001",
  "forecastDays": 30,
  "requestedBy": "admin@example.com",
  "requestedAt": "2026-07-22T09:30:00Z",
  "status": "PENDING"
}
```

---

## Processing Flow

```text
Receive SQS message
        │
        ▼
Validate request
        │
        ▼
Query Amazon Athena
        │
        ▼
Build AI prompt
        │
        ▼
Invoke Amazon Bedrock
        │
        ▼
Store result in PostgreSQL
```

---

## Retry Behavior

When message processing fails:

1. Lambda returns an error.
2. The message becomes visible again after the Visibility Timeout.
3. SQS delivers the message again.
4. After the configured maximum receive count, SQS moves the message to the Dead Letter Queue.

Example configuration:

```text
Source Queue: ai-processing-queue

Dead Letter Queue: ai-processing-dlq

Maximum receives: 3
```

---

## Partial Batch Response

Enable:

```text
Report batch item failures
```

This allows Lambda to report only the messages that failed instead of retrying the complete batch.

Example Java response:

```java
return new SQSBatchResponse(failures);
```

---

## Idempotency

Amazon SQS Standard Queues provide at-least-once delivery. Therefore, the same message may be delivered more than once.

Use a unique `requestId` and check whether the request has already been processed.

Example PostgreSQL constraint:

```sql
ALTER TABLE ai_analysis_results
ADD CONSTRAINT uk_ai_request_id UNIQUE (request_id);
```

---

## Recommended Configuration

| Configuration      | Recommended value |
| ------------------ | ----------------- |
| Batch size         | 10                |
| Lambda timeout     | 60 seconds        |
| Visibility timeout | 180 seconds       |
| Maximum receives   | 3                 |
| Message retention  | 4 days            |
| Long polling       | 20 seconds        |

---

## Verification

Send a test message to:

```text
ai-processing-queue
```

Then verify the Lambda logs in:

```text
/aws/lambda/ai-supplychain-worker
```

Expected logs:

```text
Processing request

Athena query completed

Bedrock invocation completed

AI result saved

Request completed
```

---

## Troubleshooting

| Issue                   | Possible cause                  | Resolution                             |
| ----------------------- | ------------------------------- | -------------------------------------- |
| Lambda is not invoked   | Disabled SQS trigger            | Enable the trigger                     |
| Access denied           | Missing SQS permissions         | Review the Lambda IAM Role             |
| Duplicate results       | Non-idempotent processing       | Use a unique request ID                |
| Messages reappear       | Lambda error or timeout         | Review CloudWatch Logs                 |
| Entire batch is retried | Partial batch response disabled | Enable batch item failure reporting    |
| Messages enter the DLQ  | Maximum receive count exceeded  | Review Athena, Bedrock, and RDS errors |

---

## Best Practices

- Use a unique request ID.
- Implement idempotent processing.
- Configure a Dead Letter Queue.
- Enable Partial Batch Response.
- Set Visibility Timeout higher than Lambda Timeout.
- Avoid sensitive data in message bodies.
- Encrypt queues with AWS KMS.
- Monitor SQS and Lambda metrics in CloudWatch.
- Control concurrency to protect downstream services.

---

## Expected Outcome

After completing this section, the AI Worker Lambda reliably processes asynchronous requests from Amazon SQS, retries temporary failures, isolates failed messages in a Dead Letter Queue, and prevents duplicate AI results.
