---
title: "5.3.3 Configure Security Groups"
date: 2024-01-01
weight: 3
chapter: false
---

# 5.3.3 Configure Security Groups

## Overview

After creating the VPC and Subnets, the next step is configuring **Amazon EC2 Security Groups**.

Security Groups act as virtual firewalls that control inbound and outbound traffic for AWS resources.

In the **AI Supply Chain Control Tower**, Security Groups are used to:

- Allow Lambda functions to connect to Amazon RDS.
- Prevent unauthorized access from the Internet.
- Protect the PostgreSQL database.
- Follow the AWS Well-Architected security best practices.

---

# Architecture

```
                Internet
                    │
          ─────────────────────
                    │
               API Gateway
                    │
                Lambda API
                    │
          Lambda Security Group
                    │
             PostgreSQL 5432
                    │
            Amazon RDS Security Group
                    │
               Amazon RDS
```

---

# Objectives

After completing this section, you will:

- Create a Security Group for Lambda.
- Create a Security Group for Amazon RDS.
- Configure Inbound Rules.
- Configure Outbound Rules.
- Verify Lambda-to-RDS connectivity.

---

# Why Security Groups?

Without proper Security Groups:

- Databases may be exposed to the Internet.
- Attackers can scan PostgreSQL ports.
- Backend services may fail to connect.
- Sensitive business data becomes vulnerable.

AWS recommends allowing communication only between trusted resources.

---

# Implementation Steps

## Step 1. Open EC2 Console

Navigate to:

```
EC2

Network & Security

Security Groups
```

![Kiến trúc Security](/images/5-Workshop/5.3-S3-vpc/5.3.3.png)

---

## Step 2. Create Lambda Security Group

Click:

```
Create security group
```

Configure:

| Property    | Value                     |
| ----------- | ------------------------- |
| Name        | Lambda-SG                 |
| Description | Security Group for Lambda |
| VPC         | AI-SupplyChain-VPC        |

No inbound rules are required.

Keep the default outbound rule.

Click:

```
Create Security Group
```

---

## Step 3. Create Amazon RDS Security Group

Create another Security Group:

| Property    | Value                     |
| ----------- | ------------------------- |
| Name        | RDS-SG                    |
| Description | PostgreSQL Security Group |
| VPC         | AI-SupplyChain-VPC        |

---

## Step 4. Configure Inbound Rule

Add the following rule:

| Type       | Protocol | Port | Source    |
| ---------- | -------- | ---- | --------- |
| PostgreSQL | TCP      | 5432 | Lambda-SG |

Only Lambda functions associated with **Lambda-SG** can access PostgreSQL.

> Insert Screenshot: PostgreSQL Rule

---

## Step 5. Verify Outbound Rules

For Lambda-SG:

Keep the default outbound rule:

```
All Traffic
```

This allows Lambda to communicate with Amazon RDS and other AWS services.

---

## Step 6. Verify Security Groups

The Security Group list should display:

| Name      |
| --------- |
| Lambda-SG |
| RDS-SG    |

---

# Best Practices

AWS recommends:

- Never expose PostgreSQL (5432) to the Internet.
- Avoid using `0.0.0.0/0` as the database source.
- Allow access only from trusted Security Groups.
- Follow the Principle of Least Privilege.

---

# Expected Outcome

After completing this section, you have:

- Created Security Groups for Lambda and Amazon RDS.
- Restricted PostgreSQL access to Lambda only.
- Established a secure networking layer for backend services.
