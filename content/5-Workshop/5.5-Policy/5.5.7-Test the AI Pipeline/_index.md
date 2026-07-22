---
title: "5.5.7 Test the AI Pipeline"
date: 2024-01-01
weight: 7
chapter: false
---


## Overview

This section validates the complete end-to-end AI workflow of the AI Supply Chain Control Tower.

A user submits an analysis request through the API. The request is authenticated by Amazon Cognito, processed by the Backend Lambda, published to Amazon SQS, consumed by the AI Worker Lambda, enriched with Amazon Athena data, analyzed by Amazon Bedrock, and stored in Amazon RDS PostgreSQL.

---

## Architecture

```text
User / Postman
      │
      ▼
Amazon Cognito
      │
      ▼
Amazon API Gateway
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
      │
      ▼
Dashboard / Backend API
```

---

## Objectives

After completing this section, you will:

- Submit an AI request through the API.
- Verify SQS message delivery.
- Confirm AI Worker Lambda execution.
- Validate Amazon Athena queries.
- Verify Amazon Bedrock responses.
- Confirm that AI results are stored in PostgreSQL.
- Review the complete workflow using CloudWatch Logs.

---

## Submit an AI Request

Send:

```http
POST /ai/predict
```

Request body:

```json
{
  "requestType": "DEMAND_FORECAST",
  "warehouse": "WH001",
  "forecastDays": 30
}
```

Expected response:

```json
{
  "requestId": "REQ-20260722-001",
  "status": "PENDING",
  "message": "AI analysis request submitted successfully"
}
```

Expected HTTP status:

```text
202 Accepted
```

---

## Verify Amazon SQS

Open:

```text
ai-processing-queue
```

Confirm that the message appears in the queue and is later consumed by the AI Worker Lambda.

---

## Verify AI Worker Logs

Open the CloudWatch Log Group:

```text
/aws/lambda/ai-supplychain-worker
```

Expected logs:

```text
Processing request

Athena query completed

Prompt generated

Bedrock invocation completed

AI result saved to PostgreSQL

Request completed
```

---

## Verify the Database Result

Execute:

```sql
SELECT
    request_id,
    warehouse,
    request_type,
    status,
    summary,
    recommendation,
    completed_at
FROM ai_analysis_results
ORDER BY created_at DESC;
```

Confirm that the request status is:

```text
COMPLETED
```

and that the summary and recommendation fields contain AI-generated content.

---

## Retrieve the Result through the API

Send:

```http
GET /ai/results/REQ-20260722-001
```

Expected response:

```json
{
  "requestId": "REQ-20260722-001",
  "warehouse": "WH001",
  "requestType": "DEMAND_FORECAST",
  "status": "COMPLETED",
  "summary": "Warehouse inventory is below the recommended safety stock level.",
  "recommendation": "Increase stock for low-inventory products and review delayed shipments."
}
```

---

## Failure Tests

Verify the following scenarios:

| Scenario                    | Expected result              |
| --------------------------- | ---------------------------- |
| Invalid JWT                 | HTTP 401                     |
| Invalid request body        | HTTP 400                     |
| Athena query failure        | Request retried              |
| Bedrock access denied       | Error recorded in CloudWatch |
| Database connection failure | Status updated to FAILED     |
| Repeated processing failure | Message moved to the DLQ     |

---

## Validation Checklist

| Component        | Expected result       |
| ---------------- | --------------------- |
| Amazon Cognito   | Valid Access Token    |
| API Gateway      | HTTP 202 response     |
| Backend Lambda   | SQS message published |
| Amazon SQS       | Message received      |
| AI Worker Lambda | Function invoked      |
| Amazon Athena    | Query completed       |
| Amazon Bedrock   | AI response generated |
| Amazon RDS       | Result stored         |
| Backend API      | Result returned       |
| CloudWatch       | Logs recorded         |

---

## Best Practices

- Use `202 Accepted` for asynchronous requests.
- Include the request ID in every log entry.
- Never log tokens, passwords, or secrets.
- Configure alarms for Lambda errors and DLQ messages.
- Validate AI output before using it for critical decisions.
- Monitor Athena query costs and Bedrock invocation usage.
- Repeat end-to-end tests after changing prompts or models.

---

## Expected Outcome

After completing this section, the complete AI pipeline is operational. AI requests can be submitted through the API, processed asynchronously, enriched with supply chain data, analyzed by Amazon Bedrock, stored in PostgreSQL, and retrieved by the application.
