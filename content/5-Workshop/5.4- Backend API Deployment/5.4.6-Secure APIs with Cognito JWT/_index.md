---
title: "5.4.6 Secure APIs with Amazon Cognito JWT"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.4.6 Secure APIs with Amazon Cognito JWT

## Overview

To secure backend services, Amazon Cognito issues JSON Web Tokens (JWT) after successful user authentication.

Amazon API Gateway validates the JWT before forwarding requests to the backend Lambda function. Only authenticated requests with valid access tokens are allowed to access protected resources.

---

## Architecture

```
User
 │
 ▼
Amazon Cognito
 │
 │ JWT
 ▼
API Gateway (JWT Authorizer)
 │
 ▼
Lambda Backend
 │
 ▼
Amazon RDS PostgreSQL
```

---

## Objectives

After completing this section, you will:

- Create a JWT Authorizer.
- Connect Amazon Cognito with API Gateway.
- Protect backend APIs.
- Validate JWT access tokens.
- Test authenticated requests.

---

## Implementation Steps

1. Open the Amazon API Gateway Console.
2. Create a new **JWT Authorizer**.
3. Configure:
   - Issuer URL
   - Audience (App Client ID)
   
![API Gateway Dashboard](/images/5-Workshop/5.4-S3-onprem/5.4.4.png)

4. Attach the authorizer to protected routes.
5. Deploy the updated API.
6. Sign in through Amazon Cognito to obtain an Access Token.
7. Send authenticated requests using:

```
Authorization: Bearer <Access Token>
```

---

## Expected Results

- Valid tokens return **HTTP 200 OK**.
- Invalid or expired tokens return **HTTP 401 Unauthorized**.
- API Gateway validates authentication before invoking Lambda.

---

## Best Practices

- Always use HTTPS.
- Protect sensitive APIs with JWT authorization.
- Configure appropriate token expiration.
- Use Refresh Tokens to obtain new Access Tokens.
- Keep public endpoints limited to non-sensitive operations such as health checks.

---

## Expected Outcome

After completing this section, your backend APIs are protected using Amazon Cognito JWT authentication, ensuring that only authenticated users can access protected resources.
