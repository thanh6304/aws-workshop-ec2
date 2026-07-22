---
title: "5.3.7 Create IAM Roles for AWS Lambda"
date: 2024-01-01
weight: 7
chapter: false
---


## Overview

AWS Identity and Access Management (IAM) enables secure access control for AWS resources.

In the **AI Supply Chain Control Tower**, AWS Lambda functions require access to several AWS services, including Amazon S3, Amazon SQS, Amazon Athena, AWS Glue, Amazon Bedrock, CloudWatch, and AWS Secrets Manager.

Instead of embedding AWS credentials in application code, AWS recommends assigning permissions through **IAM Roles**, following the **Principle of Least Privilege**.

---

# Architecture

```
               IAM Role
                   │
      ┌────────────┼────────────┐
      │            │            │
      ▼            ▼            ▼
 Amazon S3     Amazon SQS   CloudWatch
      │
      ▼
 Athena
      │
      ▼
 Bedrock
      │
      ▼
Secrets Manager
      │
      ▼
 Lambda Function
```

---

# Objectives

After completing this section, you will be able to:

- Create an IAM Role for AWS Lambda.
- Attach the required AWS managed policies.
- Understand the Principle of Least Privilege.
- Prepare Lambda permissions for backend and AI workloads.

---

# Implementation Steps

## Step 1. Open the IAM Console

Open the AWS Management Console.

Search for:

```
IAM
```

Navigate to:

```
Roles
```

![Kiến trúc IAM](/images/5-Workshop/5.3-S3-vpc/5.3.7.png)

---

## Step 2. Create a New Role

Click:

```
Create role
```

Choose:

- Trusted entity type: **AWS Service**
- Use case: **Lambda**

Click:

```
Next
```

---

## Step 3. Attach Permissions

Attach the following AWS managed policies:

| Policy                       | Purpose                       |
| ---------------------------- | ----------------------------- |
| AWSLambdaBasicExecutionRole  | CloudWatch logging            |
| AmazonS3FullAccess _(Demo)_  | Access Amazon S3              |
| AmazonSQSFullAccess _(Demo)_ | Send and receive SQS messages |
| AmazonAthenaFullAccess       | Execute Athena queries        |
| AWSGlueConsoleFullAccess     | Access Glue Data Catalog      |
| AmazonBedrockFullAccess      | Invoke Foundation Models      |
| SecretsManagerReadWrite      | Retrieve database credentials |

> **Note:** For production environments, replace these managed policies with custom IAM policies that grant only the permissions required by the application.

---

## Step 4. Name the Role

Configure:

| Property  | Value                     |
| --------- | ------------------------- |
| Role Name | AI-SupplyChain-LambdaRole |

Click:

```
Create Role
```

---

## Step 5. Verify the Role

Verify that:

- Trusted Entity = Lambda
- All required policies are attached successfully.

---

# Best Practices

AWS recommends:

- Never store AWS Access Keys in application code.
- Use IAM Roles instead of IAM Users for AWS services.
- Grant only the minimum permissions required.
- Review IAM permissions regularly and remove unused access.

---

# Expected Outcome

After completing this section, you have successfully created an IAM Role for AWS Lambda and granted the required permissions for backend services, analytics, and AI processing.
