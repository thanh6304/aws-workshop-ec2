---
title: "5.8.3 Test AI Processing Pipeline"
date: 2024-01-01
weight: 3
chapter: false
---



## Overview

After the Backend API stores data in Amazon RDS, it publishes a message to Amazon SQS to trigger the AI processing workflow.

In this section, you will validate the complete AI pipeline from message processing to AI-generated insights.

---

## Architecture

```text
Backend API
      │
      ▼
Amazon SQS
      │
      ▼
AI Worker Lambda
      │
      ▼
Amazon Athena
      │
      ▼
Amazon Bedrock
      │
      ▼
Amazon RDS
```

---

## Objectives

After completing this section, you will:

- Verify Amazon SQS message processing.
- Verify the AI Worker Lambda.
- Verify Amazon Athena query execution.
- Verify Amazon Bedrock inference.
- Verify AI insights stored in Amazon RDS.

---

# Step 1. Generate Test Data

Create a new order through the Backend API.

Example:

```http
POST /api/orders
```

```json
{
  "customerName": "John Smith",
  "productId": 101,
  "quantity": 10
}
```

The Backend API should:

```text
Save Order

↓

Amazon RDS

↓

Publish Message

↓

Amazon SQS
```

---

# Step 2. Verify Amazon SQS

Open:

```text
Amazon SQS
```

Select:

```text
SupplyChainQueue
```

Verify:

```text
ApproximateNumberOfMessagesVisible
```

After successful processing, the queue depth should return to:

```text
0
```

---

# Step 3. Verify AI Worker Lambda

Open:

```text
AWS Lambda

ai-worker
```

Review the CloudWatch Logs and confirm that the Lambda function was triggered successfully by Amazon SQS.

---

# Step 4. Verify Amazon Athena

Open:

```text
Amazon Athena

Query History
```

Verify that a new query completed successfully without failures.

---

# Step 5. Verify Amazon Bedrock

The AI Worker sends a prompt to Amazon Bedrock.

Example:

```text
Analyze current inventory and identify products with a high risk of stock shortages.
```

Confirm that the model returns a valid AI-generated response.

---

# Step 6. Verify Amazon RDS

Query the AI insights table.

Example:

```sql
SELECT *
FROM ai_insights
ORDER BY created_at DESC;
```

Confirm that a new analysis record has been created.

---

# Step 7. Review CloudWatch Logs

Open:

```text
CloudWatch

Log Groups

/aws/lambda/ai-worker
```

Verify:

- Lambda execution completed successfully.
- Athena queries completed successfully.
- Bedrock returned an AI response.
- No runtime errors occurred.

---

# Step 8. Test Failure Scenarios

Simulate:

- Invalid SQS messages.
- Missing input data.
- Missing IAM permissions for Athena or Bedrock.

Verify that:

- Errors are logged in CloudWatch.
- CloudWatch Metrics are updated.
- CloudWatch Alarms trigger when appropriate.

---

## Best Practices

- Design AI Workers to be idempotent.
- Configure a Dead-Letter Queue (DLQ) for failed messages.
- Configure appropriate Lambda timeouts.
- Log every processing stage for easier troubleshooting.

---

## Verification

Verify that:

- The Backend API publishes messages to Amazon SQS.
- AI Worker processes messages successfully.
- Athena queries complete successfully.
- Amazon Bedrock generates AI insights.
- Results are stored in Amazon RDS.
- CloudWatch Logs show successful execution without critical errors.

---

## Expected Outcome

After completing this section, you have:

- Validated the complete AI Processing Pipeline.
- Verified asynchronous processing using Amazon SQS.
- Confirmed integration between Lambda, Athena, and Amazon Bedrock.
- Successfully stored AI-generated insights in Amazon RDS.
