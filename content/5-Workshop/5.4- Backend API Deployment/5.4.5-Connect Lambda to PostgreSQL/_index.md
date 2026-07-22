---
title: "5.4.5 Connect Lambda to Amazon RDS PostgreSQL"
date: 2024-01-01
weight: 5
chapter: false
---

# 5.4.5 Connect Lambda to Amazon RDS PostgreSQL

## Overview

This section connects the backend Lambda function to Amazon RDS PostgreSQL using credentials securely stored in AWS Secrets Manager.

Instead of embedding database credentials in the application, Lambda retrieves the connection information at runtime, improving security and simplifying credential management.

---

## Architecture

```
Lambda Backend
       │
       │ GetSecretValue
       ▼
AWS Secrets Manager
       │
Database Credentials
       │
       ▼
Amazon RDS PostgreSQL
```

---

## Objectives

After completing this section, you will be able to:

- Connect Lambda to Amazon RDS PostgreSQL.
- Retrieve credentials from AWS Secrets Manager.
- Create database tables.
- Perform CRUD operations.
- Verify the database connection.

---

## Implementation Steps

### Step 1. Prepare the Database

Connect to Amazon RDS PostgreSQL and create the required tables.

Example:

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200),
    quantity INTEGER,
    price NUMERIC(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---
![PostgreSQL Tables](/images/5-Workshop/5.4-S3-onprem/5.4.5.png)


### Step 2. Retrieve the Secret

Use the AWS SDK to retrieve the database credentials from AWS Secrets Manager.

---

### Step 3. Configure Spring Boot

Generate the datasource configuration dynamically instead of storing credentials in `application.yml`.

---

### Step 4. Test the Connection

Deploy the Lambda function and execute a test event.

Expected response:

```json
{
  "status": "UP",
  "database": "CONNECTED"
}
```

---

### Step 5. Test CRUD Operations

Call the backend API:

```
POST /products
```

Then retrieve the data:

```
GET /products
```

Verify that the inserted record is returned successfully.

---

## Best Practices

- Store credentials in AWS Secrets Manager.
- Restrict database access using Security Groups.
- Monitor database connectivity with Amazon CloudWatch.
- Use connection pooling for production workloads.
- Encrypt sensitive data using AWS KMS.

---

## Expected Outcome

After completing this section, you have successfully connected AWS Lambda to Amazon RDS PostgreSQL, securely retrieved database credentials from AWS Secrets Manager, and verified CRUD operations through the backend API.
