---
title: "Prerequisite"
date: 2024-01-01
weight: 2
chapter: false
pre: "<b>5.2. </b>"
---

## Overview

Before deploying the **AI Supply Chain Control Tower**, we need to prepare the AWS environment, development tools, and required resources.

This workshop uses the **Asia Pacific (Singapore)** Region to deploy all AWS resources.

---

## Objectives

After completing this section, you will be able to:

- Prepare an AWS account.
- Configure AWS CLI.
- Install the required development tools.
- Connect the project source code to GitHub.
- Verify your AWS environment.

---

## Requirements

### AWS Account

- AWS Account
- IAM User with Administrator permissions (recommended)

---

### AWS Region

```
Asia Pacific (Singapore)

ap-southeast-1
```

---

### Development Tools

- Visual Studio Code
- Git
- Node.js (LTS)
- AWS CLI v2
- PostgreSQL Client
- Postman

---

### Project Source Code

Prepare:

- Frontend
- Backend
- AWS Lambda Functions

Upload the source code to GitHub for AWS Amplify deployment.

---

### Verify AWS CLI

```bash
aws --version
```

---

### Configure AWS CLI

```bash
aws configure
```

Provide:

```text
AWS Access Key ID
AWS Secret Access Key
Region: ap-southeast-1
Output format: json
```

---

### Verify Configuration

```bash
aws sts get-caller-identity
```

---

## AWS Services

The workshop uses:

- AWS Amplify
- Amazon Cognito
- Amazon API Gateway
- AWS Lambda
- Amazon RDS for PostgreSQL
- Amazon S3
- AWS Glue
- Amazon Athena
- Amazon Bedrock
- Amazon SQS
- Amazon CloudWatch
- Amazon SNS
- AWS IAM
- AWS Secrets Manager
- AWS KMS
- AWS CloudTrail
- AWS CloudFormation

---

## Expected Outcome

After completing this section, you will have:

- A ready-to-use AWS account.
- AWS CLI configured.
- Development environment prepared.
- Project source code uploaded to GitHub.
- Everything ready for infrastructure deployment.
<center>

![Frontend & Authentication](/images/5-Workshop/5.2-Workshop-overview/5.2.png)

<center>
