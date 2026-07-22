---
title: "5.4.1 Create Amazon Cognito User Pool"
date: 2024-01-01
weight: 1
chapter: false
---


## Overview

Amazon Cognito is AWS's managed identity and user authentication service.

In the **AI Supply Chain Control Tower**, Amazon Cognito authenticates users and issues **JSON Web Tokens (JWT)** that are used to authorize requests sent to Amazon API Gateway.

Using Amazon Cognito eliminates the need to build and maintain a custom authentication system while providing enterprise-grade security features.

---

# Architecture

```
User
 │
 ▼
Amazon Cognito
 │
 │ JWT Token
 ▼
Amazon API Gateway
 │
 ▼
Lambda Backend API
```

---

# Objectives

After completing this section, you will:

- Create an Amazon Cognito User Pool.
- Configure sign-in options.
- Create an App Client.
- Create a test user.
- Obtain the information required for JWT authentication.

---

# Implementation Steps

## Step 1. Open Amazon Cognito

Navigate to **Amazon Cognito** from the AWS Management Console.

Choose:

```
User Pools
```

---
![Amazon Cognito ](/images/5-Workshop/5.4-S3-onprem/5.3.4.png)

## Step 2. Create a User Pool

Click:

```
Create User Pool
```

---

## Step 3. Configure Sign-in Options

Select:

```
Email
```

as the primary sign-in method.

---

## Step 4. Configure Security

Use the default configuration:

- Password Policy: Default
- MFA: Optional or Disabled (Workshop)
- Self-registration: Enabled

---

## Step 5. Create an App Client

Configure:

| Property         | Value                 |
| ---------------- | --------------------- |
| Application Type | Public Client         |
| App Client Name  | ai-supplychain-client |

Do not generate a Client Secret for this workshop.

---

## Step 6. Name the User Pool

Set:

| Property       | Value                   |
| -------------- | ----------------------- |
| User Pool Name | ai-supplychain-userpool |

Click **Create User Pool**.

---

## Step 7. Create a Test User

Create a sample user:

| Property | Value             |
| -------- | ----------------- |
| Username | admin@example.com |
| Email    | admin@example.com |
| Password | Admin@123456      |

---

## Step 8. Save Configuration

Record the following information:

- AWS Region
- User Pool ID
- App Client ID

These values will be used when configuring API Gateway and JWT authentication.

---

# Best Practices

- Enable MFA for production deployments.
- Use strong password policies.
- Prefer OAuth 2.0 or Hosted UI for real-world applications.
- Apply the principle of least privilege when granting access.

---

# Expected Outcome

After completing this section, you have successfully created an Amazon Cognito User Pool, configured an application client, and created a test user for JWT-based authentication.
