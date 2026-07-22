---
title: "Backend API Deployment"
date: 2024-01-01
weight: 4
chapter: false
pre: "<b>5.4. </b>"
---

## Overview

After provisioning the AWS infrastructure, the next step is deploying the backend layer of the AI Supply Chain Control Tower.

The backend follows a **serverless architecture**, leveraging managed AWS services to improve scalability, availability, and operational efficiency.

This chapter covers:

- Amazon Cognito for user authentication.
- AWS Lambda for business logic.
- Amazon API Gateway for REST APIs.
- AWS Secrets Manager for secure credential storage.
- Amazon RDS PostgreSQL as the primary database.

After completing this chapter, users will be able to authenticate, invoke APIs, and securely store business data.

---

## Objectives

After completing this chapter, you will be able to:

- Create an Amazon Cognito User Pool.
- Configure AWS Secrets Manager.
- Deploy a Lambda backend.
- Configure Amazon API Gateway.
- Connect Lambda to PostgreSQL.
- Secure APIs using JWT authentication.
- Test backend APIs with Postman.

---

## Architecture

```
User
   │
   ▼
Amazon Cognito
   │
 JWT Token
   │
   ▼
Amazon API Gateway
   │
   ▼
Lambda Backend API
   │
   ├────────► AWS Secrets Manager
   │
   └────────► Amazon RDS PostgreSQL
```

---

## Contents

- 5.4.1 Create Amazon Cognito User Pool
- 5.4.2 Configure AWS Secrets Manager
- 5.4.3 Create Lambda Backend API
- 5.4.4 Configure Amazon API Gateway
- 5.4.5 Connect Lambda to PostgreSQL
- 5.4.6 Secure APIs with Cognito JWT
- 5.4.7 Test Backend APIs

---

## Expected Outcome

After completing this chapter, a fully functional serverless backend will be deployed, capable of authenticating users, processing requests, and securely interacting with Amazon RDS PostgreSQL.
