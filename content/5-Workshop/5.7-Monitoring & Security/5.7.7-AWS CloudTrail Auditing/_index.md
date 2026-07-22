---
title: "5.7.7 Audit AWS Resources with AWS CloudTrail"
date: 2024-01-01
weight: 7
chapter: false
---



## Overview

AWS CloudTrail records AWS account activity by capturing API calls made through:

- AWS Management Console
- AWS CLI
- AWS SDK
- AWS services

CloudTrail enables administrators to:

- Monitor user activity.
- Track configuration changes.
- Investigate security incidents.
- Meet auditing and compliance requirements.

In this workshop, CloudTrail records activities related to the AI Supply Chain Control Tower.

---

## Architecture

```text
Users / Applications
          │
          ▼
      AWS API Calls
          │
          ▼
     AWS CloudTrail
          │
      ┌───┴──────────┐
      ▼              ▼
 Amazon S3      CloudWatch Logs
      │              │
      ▼              ▼
 Audit Records   Monitoring
```

---

## Objectives

After completing this section, you will:

- Enable AWS CloudTrail.
- Create a Trail.
- Store audit logs in Amazon S3.
- Monitor API events.
- Search event history.

---

# Step 1. Open AWS CloudTrail

Open the AWS Management Console.

Search for:

```text
CloudTrail
```

Open:

```text
AWS CloudTrail
```

---

# Step 2. Create a Trail

Navigate to:

```text
Trails → Create trail
```

Configuration:

| Property                   | Value                 |
| -------------------------- | --------------------- |
| Trail Name                 | SupplyChainAuditTrail |
| Apply trail to all Regions | Yes                   |
| Enable log file validation | Yes                   |

---

# Step 3. Configure Log Storage

Create a new S3 bucket:

```text
ai-supplychain-cloudtrail
```

or reuse:

```text
ai-supplychain-datalake/cloudtrail/
```

CloudTrail stores audit logs as JSON files.

---

# Step 4. Enable CloudWatch Logs Integration

Enable:

```text
Send logs to CloudWatch Logs
```

Log Group:

```text
/aws/cloudtrail/supplychain
```

IAM Role:

```text
CloudTrail_CloudWatchLogs_Role
```

---

# Step 5. Configure Event Types

Enable:

```text
Management Events
```

Include:

- Read events
- Write events

This captures administrative actions performed throughout the workshop.

---

# Step 6. Generate Test Activity

Perform several actions such as:

- Opening a Lambda function.
- Viewing the RDS instance.
- Uploading an object to Amazon S3.
- Running an Athena query.

These actions generate CloudTrail events.

---

# Step 7. Review Event History

Open:

```text
Event history
```

Example events:

| Event Name          | Service             |
| ------------------- | ------------------- |
| InvokeFunction      | AWS Lambda          |
| PutObject           | Amazon S3           |
| StartQueryExecution | Amazon Athena       |
| GetSecretValue      | AWS Secrets Manager |
| SendMessage         | Amazon SQS          |

---

# Step 8. Review Event Details

Inspect an event to review:

```text
Event Time

Username

AWS Service

Source IP

Region

Request Parameters

Response Elements
```

---

# Step 9. Verify Audit Logs

Open:

```text
ai-supplychain-cloudtrail
```

or

```text
ai-supplychain-datalake/cloudtrail/
```

Verify that CloudTrail JSON log files are being generated.

---

## Best Practices

- Enable CloudTrail across all AWS Regions.
- Enable log file validation.
- Store logs in Amazon S3 and integrate with CloudWatch Logs.
- Restrict access to CloudTrail log buckets.
- Monitor sensitive API calls related to IAM, KMS, and Secrets Manager.

---

## Verification

Verify that:

- The Trail is successfully created.
- Event History displays recent API activity.
- Logs are stored in Amazon S3.
- CloudWatch Logs receives CloudTrail events.
- API calls can be searched and reviewed.

---

## Expected Outcome

After completing this section, you have:

- Enabled AWS CloudTrail for account auditing.
- Stored audit logs in Amazon S3.
- Recorded API activity across AWS services.
- Strengthened auditing and security visibility for the AI Supply Chain Control Tower.
