---
title: "5.7.5 Protect Secrets with AWS Secrets Manager"
date: 2024-01-01
weight: 5
chapter: false
---



## Overview

Applications often require sensitive information such as:

- Database usernames
- Database passwords
- API keys
- Access tokens
- Connection strings

Storing these values directly in source code or configuration files increases security risks.

AWS Secrets Manager provides a secure way to store, manage, and retrieve sensitive credentials.

In this workshop, the Backend API and AI Worker retrieve Amazon RDS PostgreSQL credentials from AWS Secrets Manager instead of hardcoding them.

---

## Architecture

```text
Backend API
        │
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

- Create a secret.
- Store database credentials.
- Grant Lambda permission to access the secret.
- Verify secret retrieval.

---

# Step 1. Open AWS Secrets Manager

From the AWS Management Console, search for:

```text
Secrets Manager
```

Open:

```text
AWS Secrets Manager
```

---

# Step 2. Create a Secret

Choose:

```text
Store a new secret
```

Secret type:

```text
Credentials for Amazon RDS database
```

Example:

```text
Username

postgres
```

```text
Password

********
```

---

# Step 3. Configure the Database Secret

Database engine:

```text
PostgreSQL
```

Select the RDS instance created earlier.

Secret name:

```text
SupplyChainDatabaseSecret
```

Description:

```text
Database credentials for AI Supply Chain Control Tower
```

Choose **Store**.

---

# Step 4. Verify the Secret

Open the secret and review:

```text
Secret Name

SupplyChainDatabaseSecret

Secret ARN

Rotation

Encryption Key
```

Secrets are encrypted with AWS KMS by default.

---

# Step 5. Grant Lambda Permission

Update the IAM policies for:

```text
BackendLambdaRole

AIWorkerLambdaRole
```

Grant:

```json
{
  "Effect": "Allow",
  "Action": ["secretsmanager:GetSecretValue"],
  "Resource": "arn:aws:secretsmanager:*:*:secret:SupplyChainDatabaseSecret-*"
}
```

---

# Step 6. Retrieve the Secret in Lambda

The Lambda function retrieves:

```text
SupplyChainDatabaseSecret
```

Flow:

```text
AWS Secrets Manager

↓

Database Credentials

↓

Amazon RDS PostgreSQL
```

No credentials are stored in source code.

---

# Step 7. Verify CloudWatch Logs

Invoke the Backend API.

Open:

```text
CloudWatch

Log Groups

/aws/lambda/backend-api
```

Verify that:

- The secret is retrieved successfully.
- The database connection succeeds.
- No **AccessDeniedException** errors occur.

---

# Step 8. Review Secret Metadata

Review:

```text
Secret Name

KMS Key

Last Changed Date

Last Accessed Date
```

These details help administrators audit secret usage.

---

## Best Practices

- Never store passwords in source code.
- Store sensitive information in AWS Secrets Manager.
- Grant only `GetSecretValue` permission to required IAM roles.
- Enable automatic rotation when supported.
- Monitor secret access with AWS CloudTrail.

---

## Verification

Verify that:

- The secret is created successfully.
- Lambda can retrieve the secret.
- Backend API connects to Amazon RDS.
- No permission errors appear in CloudWatch Logs.

---

## Expected Outcome

After completing this section, you have:

- Stored database credentials securely in AWS Secrets Manager.
- Granted secure access to Lambda functions.
- Removed sensitive credentials from application code.
- Improved the overall security posture of the AI Supply Chain Control Tower.
