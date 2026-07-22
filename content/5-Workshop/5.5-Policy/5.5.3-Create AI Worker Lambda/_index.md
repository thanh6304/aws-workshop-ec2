---
title: "5.5.3 Deploy AI Worker Lambda"
date: 2024-01-01
weight: 3
chapter: false
---

# 5.5.3 Deploy AI Worker Lambda

## Overview

The AI Worker Lambda is responsible for processing asynchronous AI requests received from Amazon SQS.

Instead of executing AI tasks within the backend API, requests are placed into a queue. The AI Worker consumes these messages, queries business data using Amazon Athena, invokes Amazon Bedrock for AI analysis, and stores the generated insights in Amazon RDS PostgreSQL.

This architecture improves scalability, fault tolerance, and API responsiveness.

---

## Architecture

```
Amazon SQS
      │
      ▼
AI Worker Lambda
      │
 ┌────┼──────────────┐
 ▼    ▼              ▼
Athena Bedrock   Amazon RDS
```

---

## Objectives

After completing this section, you will:

- Create an AI Worker Lambda.
- Configure Amazon SQS as an event source.
- Set environment variables.
- Prepare the Lambda for Athena and Bedrock integration.

---

## Implementation Steps

1. Create a Lambda function named:

```
ai-supplychain-worker
```

2. Configure:

- Runtime: Java 21
- Execution Role: AI-SupplyChain-LambdaRole
- Private Subnets
- Lambda Security Group
![Lambda function ](/images/5-Workshop/5.5-Policy/5.5.3.png)

3. Add an Amazon SQS Trigger:

```
ai-processing-queue
```

4. Configure environment variables:

- ATHENA_DATABASE
- ATHENA_OUTPUT
- SECRET_NAME
- AWS_REGION

5. Set:

- Memory: 1024 MB
- Timeout: 60 seconds

6. Upload the Lambda package and verify the SQS trigger.

---

## Best Practices

- Configure an appropriate batch size.
- Use a Dead Letter Queue (DLQ) for failed messages.
- Monitor execution using Amazon CloudWatch.
- Apply least-privilege IAM permissions.
- Allocate sufficient memory and timeout for AI workloads.

---

## Expected Outcome

After completing this section, the AI Worker Lambda is deployed, connected to Amazon SQS, and ready to query Amazon Athena and integrate with Amazon Bedrock for AI processing.
