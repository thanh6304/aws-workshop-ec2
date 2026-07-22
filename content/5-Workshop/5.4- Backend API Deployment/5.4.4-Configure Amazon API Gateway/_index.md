---
title: "5.4.4 Configure Amazon API Gateway"
date: 2024-01-01
weight: 4
chapter: false
---


## Overview

Amazon API Gateway is a fully managed service for creating, publishing, maintaining, monitoring, and securing APIs at any scale.

In the **AI Supply Chain Control Tower**, API Gateway serves as the entry point for all client requests. It forwards incoming HTTP requests to the backend Lambda function and returns the processed responses to the client.

---

## Architecture

```
Frontend
     │
     ▼
Amazon API Gateway
     │
     ▼
Lambda Backend API
     │
     ▼
Amazon RDS PostgreSQL
```

---

## Objectives

After completing this section, you will:

- Create an HTTP API.
- Integrate the API with AWS Lambda.
- Deploy the API.
- Obtain the API endpoint.
- Verify the deployment.

---

## Implementation Steps

1. Open the **Amazon API Gateway Console**.
![API Gateway Dashboard](/images/5-Workshop/5.4-S3-onprem/5.4.4.png)

2. Create a new **HTTP API**.
3. Add an integration with the Lambda function `ai-supplychain-backend`.
4. Create the following routes:
   - GET `/health`
   - GET `/products`
   - POST `/products`
   - GET `/orders`
   - POST `/orders`
   - POST `/ai/predict`
5. Name the API `AI-SupplyChain-API`.
6. Deploy the API using the default stage.
7. Test the endpoint with a browser or Postman.

---

## Best Practices

- Use HTTP APIs for lower latency and lower cost when REST API features are unnecessary.
- Protect endpoints with JWT authorization.
- Enable CloudWatch logging.
- Organize API routes consistently.

---

## Expected Outcome

After completing this section, you have successfully deployed an Amazon API Gateway, integrated it with AWS Lambda, and exposed secure HTTP endpoints for the backend application.
