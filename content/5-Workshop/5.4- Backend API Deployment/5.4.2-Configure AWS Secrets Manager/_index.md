---
title: "5.4.2 Configure AWS Secrets Manager"
date: 2024-01-01
weight: 2
chapter: false
---


## Overview

AWS Secrets Manager is a managed service for securely storing and managing sensitive information such as database credentials, API keys, OAuth tokens, and encryption secrets.

In the **AI Supply Chain Control Tower**, the backend Lambda function retrieves database credentials from AWS Secrets Manager instead of storing them directly in the application code.

This approach improves security and aligns with AWS security best practices.

---

## Architecture

```
Lambda Backend
        │
 GetSecretValue
        ▼
AWS Secrets Manager
        │
        ▼
Database Credentials
        │
        ▼
Amazon RDS PostgreSQL
```

---

## Objectives

After completing this section, you will:

- Create a new secret.
- Store PostgreSQL connection information.
- Configure Lambda access permissions.
- Verify that the secret has been created successfully.

---

## Implementation Steps

### Step 1. Open AWS Secrets Manager

Navigate to **AWS Secrets Manager** in the AWS Management Console.
![AI Supply Chain Architecture](/images/5-Workshop/5.4-S3-onprem/5.4.2.png)

### Step 2. Create a New Secret

Click:

```
Store a new secret
```

### Step 3. Select Secret Type

Choose:

```
Credentials for Amazon RDS database
```

or **Other type of secret** if you prefer to enter the values manually as JSON.

### Step 4. Enter Database Credentials

Store the following values:

```json
{
  "username": "postgres",
  "password": "YourStrongPassword",
  "host": "ai-supplychain-db.xxxxxxxxx.ap-southeast-1.rds.amazonaws.com",
  "port": 5432,
  "database": "supplychain"
}
```

### Step 5. Configure Secret Details

| Property    | Value                    |
| ----------- | ------------------------ |
| Secret Name | ai-supplychain-db-secret |

### Step 6. Configure Rotation

Disable automatic rotation for this workshop.

For production environments, enable automatic secret rotation.

### Step 7. Store the Secret

Click **Store**.

---

## Lambda Permission

Grant the Lambda execution role permission to retrieve the secret:

```json
{
  "Effect": "Allow",
  "Action": ["secretsmanager:GetSecretValue"],
  "Resource": "arn:aws:secretsmanager:*:*:secret:ai-supplychain-db-secret*"
}
```

---

## Best Practices

- Never hard-code credentials in source code.
- Store secrets in AWS Secrets Manager.
- Enable automatic rotation for production.
- Grant only the required IAM permissions.
- Use IAM Roles instead of long-lived AWS access keys.

---

## Expected Outcome

After completing this section, you have securely stored your PostgreSQL credentials in AWS Secrets Manager and prepared your Lambda functions to retrieve them securely at runtime.
