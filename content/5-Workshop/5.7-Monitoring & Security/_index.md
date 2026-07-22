---
title: "5.7 Monitoring & Security"
date: 2024-01-01
weight: 7
chapter: true
pre: "<b>5.7. </b>"
---

## Overview

After deploying the **AI Supply Chain Control Tower**, monitoring system health, detecting operational issues, and securing AWS resources are essential to maintaining a reliable and secure production environment.

In this chapter, you will use several AWS services to monitor application performance, configure automated alerts, manage access permissions, protect sensitive information, encrypt data, and audit activities across your AWS account.

This chapter covers:

- Monitoring with Amazon CloudWatch.
- Configuring CloudWatch Alarms.
- Sending notifications with Amazon SNS.
- Managing permissions using AWS Identity and Access Management (IAM).
- Protecting secrets with AWS Secrets Manager.
- Encrypting data with AWS Key Management Service (AWS KMS).
- Auditing AWS resources using AWS CloudTrail.
- Validating the monitoring and security configuration.

---

## Architecture

```text
                    AI Supply Chain Control Tower
                               │
        ┌──────────────────────┼──────────────────────┐
        ▼                      ▼                      ▼
   API Gateway             Lambda API          Lambda AI Worker
        │                      │                      │
        └──────────────┬───────┴──────────────┬───────┘
                       ▼                      ▼
                  Amazon CloudWatch      Amazon CloudTrail
                       │                      │
        ┌──────────────┼──────────────┐       │
        ▼              ▼              ▼       ▼
     Metrics         Logs         Alarms   Audit Logs
        │                             │
        ▼                             ▼
 Amazon SNS                     Email Notification
        │
        ▼
 Administrator

Security Services

IAM
Secrets Manager
AWS KMS
```

---

## Objectives

After completing this chapter, you will be able to:

- Monitor system health using Amazon CloudWatch.
- Collect and analyze metrics and logs.
- Configure CloudWatch Alarms to detect operational issues.
- Send automated notifications through Amazon SNS.
- Manage access permissions using AWS IAM.
- Protect sensitive information with AWS Secrets Manager.
- Encrypt data using AWS Key Management Service (AWS KMS).
- Audit AWS account activity with AWS CloudTrail.
- Validate the overall monitoring and security configuration.

---

## Contents

This chapter contains the following hands-on labs:

- **5.7.1** Monitor the System with Amazon CloudWatch
- **5.7.2** Configure CloudWatch Alarms
- **5.7.3** Configure Amazon SNS Notifications
- **5.7.4** Secure Access with AWS Identity and Access Management (IAM)
- **5.7.5** Protect Secrets with AWS Secrets Manager
- **5.7.6** Encrypt Data Using AWS Key Management Service (AWS KMS)
- **5.7.7** Audit AWS Resources with AWS CloudTrail
- **5.7.8** Validate Monitoring and Security Configuration

---

## Expected Outcome

After completing this chapter, the AI Supply Chain Control Tower will be able to:

- Monitor application performance in real time.
- Collect and retain operational logs.
- Detect issues and send automated alerts.
- Protect sensitive data and application secrets.
- Enforce least-privilege access control.
- Audit AWS account activities for compliance and incident investigation.
