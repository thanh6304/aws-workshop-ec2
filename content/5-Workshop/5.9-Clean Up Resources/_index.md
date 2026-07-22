---
title: "5.9 Clean Up Resources"
date: 2024-01-01
weight: 9
chapter: false
---

## Overview

After completing the workshop, you should remove all AWS resources created during the deployment to avoid unnecessary charges.

This section walks through the recommended cleanup sequence to safely remove resources while avoiding dependency issues.

---

## Architecture

```text
AWS Amplify
      │
Amazon Cognito
      │
API Gateway
      │
Lambda Functions
      │
Amazon SQS
      │
Amazon RDS
      │
Amazon S3
      │
AWS Glue
      │
Amazon Athena
      │
CloudWatch
      │
Amazon SNS
      │
AWS CloudTrail
```

---

## Objectives

After completing this section, you will:

- Remove all deployed AWS resources.
- Prevent unnecessary AWS charges.
- Verify that your AWS account has been completely cleaned up.

---

# Step 1. Delete the AWS Amplify Application

Open **AWS Amplify**, select the **AI Supply Chain Control Tower** application, choose **Actions**, and then select **Delete App**.

---

# Step 2. Delete Amazon Cognito Resources

Open **Amazon Cognito** → **User Pools**.

Delete the **SupplyChainUserPool**.

---

# Step 3. Delete API Gateway

Open **Amazon API Gateway**.

Delete the HTTP API created for this workshop.

---

# Step 4. Delete Lambda Functions

Delete the following Lambda functions:

```text
backend-api

ai-worker
```

---

# Step 5. Delete Amazon SQS

Delete the queue:

```text
SupplyChainQueue
```

---

# Step 6. Delete Amazon RDS

Delete the database instance:

```text
supplychain-db
```

Skip the final snapshot if it is no longer required.

---

# Step 7. Delete Amazon S3

Open the bucket:

```text
ai-supplychain-datalake
```

First empty the bucket, then delete it.

---

# Step 8. Delete AWS Glue and Amazon Athena Resources

Remove:

- Glue Database
- Glue Tables
- Glue Crawlers

Also clean up Athena query results stored in Amazon S3 if they are no longer needed.

---

# Step 9. Delete AWS Secrets Manager Secrets

Delete:

```text
SupplyChainDatabaseSecret
```

---

# Step 10. Delete Amazon SNS

Delete the topic:

```text
SupplyChainAlerts
```

---

# Step 11. Delete AWS CloudTrail

Delete:

```text
SupplyChainAuditTrail
```

Delete its dedicated S3 bucket if one was created exclusively for CloudTrail logs.

---

# Step 12. Delete CloudWatch Resources

Remove:

- Dashboards
- Alarms
- Log Groups

Examples:

```text
/aws/lambda/backend-api

/aws/lambda/ai-worker
```

---

# Step 13. Delete IAM Roles

Delete workshop-specific IAM roles such as:

```text
BackendLambdaRole

AIWorkerLambdaRole
```

Only remove roles that are no longer in use.

---

# Step 14. Verify AWS Billing

Open **AWS Billing** → **Bills**.

Verify that:

- No workshop resources remain active.
- No unexpected charges are being incurred.

---

## Best Practices

- Remove workshop resources immediately after completion.
- Empty Amazon S3 buckets before deleting them.
- Delete IAM roles and KMS keys only if they are no longer required.
- Monitor AWS Billing for several days after cleanup to ensure no resources remain.

---

## Verification

Confirm that:

- AWS Amplify has been removed.
- Amazon Cognito has been deleted.
- API Gateway no longer exists.
- Lambda functions have been removed.
- Amazon RDS has been deleted.
- Amazon S3 buckets have been removed.
- AWS Glue and Athena resources have been cleaned up.
- Amazon SNS, CloudTrail, and CloudWatch resources have been removed.
- No workshop resources remain in your AWS account.

---

## Expected Outcome

Congratulations!

You have successfully completed the **AI Supply Chain Control Tower on AWS** workshop.

Throughout this workshop, you have:

- Built a serverless application on AWS.
- Deployed a backend API using AWS Lambda.
- Created a data lake with Amazon S3 and AWS Glue.
- Performed analytics using Amazon Athena.
- Integrated Generative AI using Amazon Bedrock.
- Implemented monitoring and security with CloudWatch, IAM, Secrets Manager, AWS KMS, and CloudTrail.
- Validated the complete end-to-end solution.
- Cleaned up all AWS resources to minimize operational costs.

Your AWS environment is now clean and ready for future projects or experiments.
