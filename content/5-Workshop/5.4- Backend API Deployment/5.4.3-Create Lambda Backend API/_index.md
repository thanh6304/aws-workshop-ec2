---
title: "5.4.3 Deploy Lambda Backend API"
date: 2024-01-01
weight: 3
chapter: false
---

# 5.4.3 Deploy Lambda Backend API

## Overview

AWS Lambda is a serverless compute service that allows you to run code without provisioning or managing servers.

In the **AI Supply Chain Control Tower**, the backend Lambda function acts as the business logic layer. It processes API requests, retrieves database credentials from AWS Secrets Manager, connects to Amazon RDS PostgreSQL, publishes AI tasks to Amazon SQS, and writes execution logs to Amazon CloudWatch.

---

## Architecture

```
Client
   │
   ▼
Amazon API Gateway
   │
   ▼
Lambda Backend API
   │
   ├────────► AWS Secrets Manager
   ├────────► Amazon RDS PostgreSQL
   ├────────► Amazon SQS
   └────────► Amazon CloudWatch
```

---

## Objectives

After completing this section, you will:

- Create a Lambda function.
- Configure the runtime and execution role.
- Connect Lambda to the VPC.
- Configure environment variables.
- Deploy the application package.
- Verify the function using a test event.

---

## Implementation Steps

1. Open the **AWS Lambda Console**.
![Lambda Dashboard](/images/5-Workshop/5.4-S3-onprem/5.4.2.png)

2. Create a new function using **Author from scratch**.
3. Configure:
   - Function Name: `ai-supplychain-backend`
   - Runtime: **Java 21** (or your project runtime)
4. Attach the existing IAM Role: `AI-SupplyChain-LambdaRole`.
5. Connect the function to the VPC using the private subnets and Lambda security group.
6. Add environment variables:
   - `SECRET_NAME`
   - `AWS_REGION`
   - `QUEUE_NAME`
7. Upload the deployment package (.zip or .jar).
8. Configure the correct handler.
9. Set Memory to **1024 MB** and Timeout to **30 seconds**.
10. Deploy the function.
11. Create a test event and verify that the function returns a successful response.

---

## Best Practices

- Store credentials in AWS Secrets Manager instead of environment variables.
- Connect Lambda to a VPC only when private resources such as Amazon RDS are required.
- Configure memory and timeout based on workload requirements.
- Monitor executions with Amazon CloudWatch Logs and Metrics.

---

## Expected Outcome

After completing this section, you have successfully deployed the backend Lambda function and prepared it to securely interact with Amazon RDS PostgreSQL, Amazon SQS, and other AWS services.
