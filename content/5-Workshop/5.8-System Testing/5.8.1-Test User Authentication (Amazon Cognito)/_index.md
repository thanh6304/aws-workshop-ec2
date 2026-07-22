---
title: "5.8.1 Test User Authentication with Amazon Cognito"
date: 2024-01-01
weight: 1
chapter: false
---

# 5.8.1 Test User Authentication with Amazon Cognito

## Overview

Before users can access the AI Supply Chain Control Tower, they must authenticate through Amazon Cognito.

In this section, you will verify the login process and ensure that Amazon Cognito issues a valid JSON Web Token (JWT) for accessing the Backend API.

---

## Architecture

```text
User
   │
   ▼
Frontend (AWS Amplify)
   │
   ▼
Amazon Cognito
   │
JWT Access Token
   │
   ▼
API Gateway
```

---

## Objectives

After completing this section, you will:

- Sign in using Amazon Cognito.
- Obtain a JWT Access Token.
- Inspect the token.
- Verify that API Gateway accepts valid tokens.

---

# Step 1. Open the Application

Open the application deployed on AWS Amplify.

Example:

```text
https://your-app.amplifyapp.com
```

The sign-in page should appear.

---

# Step 2. Sign In

Enter a valid Cognito user account.

Example:

```text
Username

admin@example.com
```

```text
Password

********
```

Choose:

```text
Sign In
```

---

# Step 3. Verify Successful Login

After signing in successfully:

- The user is redirected to the dashboard.
- A user session is established.
- Amazon Cognito issues JWT tokens.

---

# Step 4. Inspect JWT Tokens

Open your browser's Developer Tools.

Navigate to:

```text
Application

Local Storage
```

or

```text
Session Storage
```

Verify the existence of:

```text
Access Token

ID Token

Refresh Token
```

---

# Step 5. Validate the Access Token

Copy the Access Token and decode it using a JWT decoder.

Verify fields such as:

```text
sub

username

email

exp

iat
```

Ensure the token is valid and has not expired.

---

# Step 6. Test API Gateway Authorization

Send a request to the Backend API with:

```text
Authorization:
Bearer <Access Token>
```

Expected result:

- API Gateway validates the token.
- The request is forwarded to the Backend Lambda.
- The API returns a successful response.

---

# Step 7. Test Invalid Tokens

Try:

- Removing the token.
- Modifying one character.
- Using an expired token.

Expected response:

```text
401 Unauthorized
```

This confirms that API Gateway rejects invalid authentication tokens.

---

# Step 8. Review CloudWatch Logs

Open:

```text
CloudWatch

Log Groups

/API-Gateway-Execution-Logs

/aws/lambda/backend-api
```

Verify:

- Successful authentication requests are logged.
- No unexpected authentication errors occur.

---

## Best Practices

- Always transmit JWTs over HTTPS.
- Do not expose tokens in client-side logs.
- Use short-lived Access Tokens with Refresh Tokens.
- Validate token expiration before sending API requests.

---

## Verification

Verify that:

- Users can successfully sign in.
- Amazon Cognito issues JWT tokens.
- API Gateway accepts valid tokens.
- Invalid or expired tokens return **401 Unauthorized**.
- CloudWatch Logs capture authentication requests.

---

## Expected Outcome

After completing this section, you have:

- Successfully validated user authentication with Amazon Cognito.
- Verified JWT-based authorization for API Gateway.
- Confirmed that the authentication layer is functioning correctly before testing the Backend API.
