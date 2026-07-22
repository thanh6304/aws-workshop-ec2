---
title: "5.4.7 Test Backend APIs"
date: 2024-01-01
weight: 7
chapter: false
---


## Overview

This section validates the complete backend workflow of the AI Supply Chain Control Tower using Postman.

The following components will be verified:

- Amazon Cognito authentication
- Amazon API Gateway
- AWS Lambda execution
- AWS Secrets Manager
- Amazon RDS PostgreSQL
- Amazon SQS
- Amazon CloudWatch

---

## Objectives

After completing this section, you will be able to:

- Test the Health Check endpoint.
- Verify JWT authentication.
- Perform CRUD operations.
- Confirm database connectivity.
- Verify SQS message delivery.
- Review execution logs in CloudWatch.

---

## Test Cases

### Test 1 — Health Check

```
GET /health
```

Expected:

```json
{
  "status": "UP"
}
```

---

### Test 2 — JWT Authentication

Authenticate through Amazon Cognito.

Send:

```
Authorization: Bearer <AccessToken>
```

Expected:

```
HTTP 200 OK
```

Invalid tokens should return:

```
HTTP 401 Unauthorized
```

---

### Test 3 — Create Product

```
POST /products
```

Verify that the product is inserted successfully.

---

### Test 4 — Retrieve Products

```
GET /products
```

Confirm the inserted product is returned.

---

### Test 5 — AI Prediction Request

```
POST /ai/predict
```

Verify that the backend publishes a message to Amazon SQS.

---

### Test 6 — Verify Amazon SQS

Open the Amazon SQS Console and confirm that the message appears in the queue.

---

### Test 7 — Verify CloudWatch Logs

Open the Lambda Log Group and verify successful execution logs.

---

## Troubleshooting

| Issue                      | Possible Cause                  | Solution                     |
| -------------------------- | ------------------------------- | ---------------------------- |
| 401 Unauthorized           | Invalid or expired JWT          | Authenticate again           |
| 500 Internal Server Error  | Lambda execution error          | Review CloudWatch Logs       |
| Database Connection Failed | Incorrect VPC or Security Group | Verify network configuration |
| Access Denied              | Missing IAM permissions         | Review IAM Role              |
| Queue Not Found            | Incorrect queue name            | Verify environment variables |

---

## Expected Outcome

After completing this section, you have validated the complete backend architecture, including authentication, API Gateway, Lambda, database access, asynchronous messaging, and monitoring.
