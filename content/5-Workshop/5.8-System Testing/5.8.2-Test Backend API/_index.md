---
title: "5.8.2 Test Backend API"
date: 2024-01-01
weight: 2
chapter: false
---

# 5.8.2 Test Backend API

## Overview

After users successfully authenticate with Amazon Cognito, the next step is to validate the Backend API.

In this section, you will verify that:

- API Gateway receives client requests.
- JWT authentication succeeds.
- Backend Lambda processes requests.
- Amazon RDS stores and retrieves data correctly.
- The API returns the expected responses.

---

## Architecture

```text
User
   │
JWT
   │
   ▼
API Gateway
   │
   ▼
Backend Lambda
   │
   ▼
Amazon RDS PostgreSQL
```

---

## Objectives

After completing this section, you will:

- Send authenticated API requests.
- Verify API responses.
- Validate data stored in Amazon RDS.
- Review CloudWatch Logs.

---

# Step 1. Obtain an Access Token

Sign in through Amazon Cognito.

Copy the:

```text
Access Token
```

Use it in the Authorization header for API requests.

---

# Step 2. Test the Health Endpoint

Send:

```http
GET /health
```

Example:

```http
GET https://your-api.execute-api.region.amazonaws.com/health
```

Expected response:

```json
{
  "status": "UP"
}
```

---

# Step 3. Test an Authenticated Request

Example:

```http
GET /api/inventory
```

Header:

```http
Authorization:
Bearer <Access Token>
```

Expected response:

```http
200 OK
```

Example:

```json
[
  {
    "productId": 101,
    "productName": "Laptop",
    "quantity": 250
  }
]
```

---

# Step 4. Test Data Creation

Send:

```http
POST /api/orders
```

Example payload:

```json
{
  "customerName": "John Smith",
  "productId": 101,
  "quantity": 5
}
```

Expected response:

```http
201 Created
```

The backend should:

```text
Receive Request

↓

Validate Data

↓

Store in Amazon RDS

↓

Return Success Response
```

---

# Step 5. Verify Database Records

Query the database:

```sql
SELECT *
FROM orders
ORDER BY created_at DESC;
```

Confirm that the new record has been inserted successfully.

---

# Step 6. Review CloudWatch Logs

Open:

```text
CloudWatch

Log Groups

/aws/lambda/backend-api
```

Verify:

- Requests are logged.
- No runtime exceptions occur.
- No database connection errors are present.

---

# Step 7. Test Validation Errors

Send an invalid request.

Example:

```json
{
  "quantity": -10
}
```

Expected response:

```http
400 Bad Request
```

Example:

```json
{
  "message": "Validation failed"
}
```

---

# Step 8. Test Unauthorized Requests

Send a request without an Authorization header.

Expected response:

```http
401 Unauthorized
```

---

## Best Practices

- Validate all user input before database operations.
- Return appropriate HTTP status codes.
- Avoid exposing sensitive information in API responses.
- Log operational errors for troubleshooting.

---

## Verification

Verify that:

- API Gateway forwards requests successfully.
- Backend Lambda executes correctly.
- Amazon RDS stores and retrieves data accurately.
- CloudWatch Logs capture API activity.
- HTTP status codes match the expected behavior.

---

## Expected Outcome

After completing this section, you have:

- Verified the Backend API functionality.
- Validated database read and write operations.
- Confirmed successful integration between API Gateway, Lambda, and Amazon RDS.
