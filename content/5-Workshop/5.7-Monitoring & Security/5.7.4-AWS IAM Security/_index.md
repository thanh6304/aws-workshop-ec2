---
title: "5.7.4 Secure Access with AWS Identity and Access Management (IAM)"
date: 2024-01-01
weight: 4
chapter: false
---


## Overview

AWS Identity and Access Management (IAM) enables secure access control for AWS resources.

Instead of granting broad administrative permissions, IAM allows you to create policies that provide only the permissions required for each user or application.

In this workshop, IAM is used to:

- Manage administrative users.
- Grant permissions to the Backend Lambda.
- Grant permissions to the AI Worker Lambda.
- Control access to Amazon S3, Amazon SQS, Amazon Athena, and Amazon Bedrock.
- Apply the Principle of Least Privilege.

---

## Architecture

```text
IAM Users
      │
      ▼
IAM Policies
      │
      ▼
IAM Roles
      │
      ├── Backend Lambda
      ├── AI Worker Lambda
      └── Administrator
              │
              ▼
AWS Resources

Amazon S3
Amazon SQS
Amazon Athena
Amazon Bedrock
AWS Secrets Manager
Amazon CloudWatch
Amazon RDS
```

---

## Objectives

After completing this section, you will:

- Create IAM users.
- Create IAM policies.
- Create IAM roles.
- Attach roles to Lambda functions.
- Verify AWS resource access.

---

# Step 1. Open the IAM Console

Open the AWS Management Console.

Search for:

```text
IAM
```

Select:

```text
Identity and Access Management (IAM)
```

---

# Step 2. Create an IAM User

Navigate to:

```text
Users → Create user
```

User name:

```text
SupplyChainAdmin
```

Enable AWS Management Console access and configure a password according to your organization's policy.

---

# Step 3. Create a Backend Lambda Policy

Create a custom IAM policy that allows:

- CloudWatch Logs access
- Amazon S3 object read/write
- Amazon SQS message publishing

Save the policy as:

```text
BackendLambdaPolicy
```

---

# Step 4. Create an AI Worker Policy

Create a custom policy that grants:

- Amazon Athena query execution
- Amazon Bedrock model invocation
- Amazon S3 read access
- Amazon CloudWatch Logs access

Save the policy as:

```text
AIWorkerPolicy
```

---

# Step 5. Create the Backend Lambda Role

Create a new IAM role.

Trusted entity:

```text
AWS Service → Lambda
```

Attach:

```text
BackendLambdaPolicy
```

Role name:

```text
BackendLambdaRole
```

---

# Step 6. Create the AI Worker Role

Create another Lambda role.

Attach:

```text
AIWorkerPolicy
```

Role name:

```text
AIWorkerLambdaRole
```

---

# Step 7. Attach Roles to Lambda

Open the Lambda console.

Assign:

```text
BackendLambdaRole
```

to:

```text
backend-api
```

Assign:

```text
AIWorkerLambdaRole
```

to:

```text
ai-worker
```

---

# Step 8. Verify Permissions

Invoke both Lambda functions and confirm they can:

- Write logs to Amazon CloudWatch.
- Publish messages to Amazon SQS.
- Read data from Amazon S3.
- Execute Amazon Athena queries.
- Invoke Amazon Bedrock models.

If permissions are missing, CloudWatch Logs will display an **AccessDeniedException**.

---

## Best Practices

- Follow the Principle of Least Privilege.
- Avoid attaching `AdministratorAccess` to Lambda execution roles.
- Create separate roles for different workloads.
- Review IAM policies regularly.
- Use CloudWatch Logs and AWS CloudTrail to troubleshoot access issues.

---

## Verification

Verify that:

- IAM users, policies, and roles have been created.
- Lambda functions use the correct execution roles.
- Required AWS services are accessible.
- No **AccessDeniedException** errors occur during execution.

---

## Expected Outcome

After completing this section, you have:

- Created an IAM administrative user.
- Built least-privilege IAM policies.
- Configured dedicated IAM roles for the Backend API and AI Worker.
- Secured AWS resource access for the AI Supply Chain Control Tower.
