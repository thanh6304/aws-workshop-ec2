---
title: "5.3.4 Deploy Amazon RDS PostgreSQL"
date: 2024-01-01
weight: 4
chapter: false
---

# 5.3.4 Deploy Amazon RDS PostgreSQL

## Overview

Amazon Relational Database Service (Amazon RDS) is a fully managed relational database service that simplifies database deployment, operation, and scaling in the cloud.

In the **AI Supply Chain Control Tower**, Amazon RDS for PostgreSQL stores operational data such as user accounts, inventory, orders, AI analysis results, and system logs.

By using Amazon RDS, we benefit from automated backups, monitoring, software patching, and high availability options without managing database infrastructure manually.

---

## Architecture

```
Lambda API
      │
 PostgreSQL (5432)
      ▼
Amazon RDS PostgreSQL
      │
Private Subnet A
Private Subnet B
```

The database is deployed inside **Private Subnets**, ensuring it is not directly accessible from the Internet.

---

## Objectives

After completing this section, you will be able to:

- Create a DB Subnet Group.
- Deploy an Amazon RDS PostgreSQL instance.
- Configure networking and security.
- Create the initial database.
- Retrieve the database endpoint for backend integration.

---

## Implementation Steps

### Step 1. Open the Amazon RDS Console

Navigate to **Amazon RDS** in the AWS Management Console and open the **Databases** page.

### Step 2. Create a DB Subnet Group

Create a subnet group using the two previously created Private Subnets.

### Step 3. Create a Database

Select **Create database** and choose **Standard Create**.

### Step 4. Select the Database Engine

Choose **PostgreSQL** and select the latest stable engine version.

### Step 5. Configure Database Settings

Set:

- DB Instance Identifier: `ai-supplychain-db`
- Master Username: `postgres`
- Master Password: `<your-password>`

### Step 6. Configure Compute and Storage

- Instance Class: `db.t3.micro`
- Storage Type: `gp3`
- Allocated Storage: `20 GB`

### Step 7. Configure Networking

- VPC: `AI-SupplyChain-VPC`
- DB Subnet Group: `ai-supplychain-subnet-group`
- Public Access: `No`
- Security Group: `RDS-SG`

### Step 8. Configure Additional Settings

Create an initial database named:

```
supplychain
```

### Step 9. Launch the Database

Click **Create Database** and wait until the instance status becomes **Available**.

### Step 10. Retrieve the Endpoint

From the **Connectivity & Security** tab, copy the RDS Endpoint. This endpoint will be used later by the backend Lambda function to establish a secure database connection.

---

## Best Practices

- Deploy the database inside Private Subnets.
- Disable Public Access.
- Restrict port **5432** to trusted Security Groups only.
- Store database credentials in **AWS Secrets Manager**.
- Enable automated backups for disaster recovery.

---

## Expected Outcome

After completing this section, you have successfully deployed an Amazon RDS PostgreSQL instance inside your VPC and prepared it for secure integration with the backend services.