---
title: "5.7.1 Monitor the System with Amazon CloudWatch"
date: 2024-01-01
weight: 1
chapter: false
---

# 5.7.1 Monitor the System with Amazon CloudWatch

## Overview

After deploying the AI Supply Chain Control Tower on AWS, continuous monitoring is essential to ensure the application remains healthy and performs as expected.

Amazon CloudWatch is AWS's monitoring service that collects:

- Metrics
- Logs
- Events
- Dashboards
- Alarms

In this workshop, CloudWatch will monitor:

- AWS Lambda
- Amazon API Gateway
- Amazon RDS
- Amazon SQS
- Amazon Athena
- Amazon Bedrock

---

## Architecture

```text
Application
        │
        ▼
AWS Services
        │
        ├── Lambda
        ├── API Gateway
        ├── RDS
        ├── SQS
        ├── Athena
        └── Bedrock
                │
                ▼
       Amazon CloudWatch
                │
        ┌───────┴────────┐
        ▼                ▼
     Metrics         Log Groups
                │
                ▼
      Dashboards & Alarms
```

---

## Objectives

After completing this section, you will:

- Open Amazon CloudWatch.
- Review service metrics.
- Inspect application logs.
- Monitor Lambda functions.
- Monitor API Gateway.
- Monitor Amazon RDS.
- Monitor Amazon SQS.
- Prepare for CloudWatch Alarms.

---

# Step 1. Open Amazon CloudWatch

Sign in to the AWS Management Console.

Search for:

```text
CloudWatch
```

Open:

```text
Amazon CloudWatch
```

---

# Step 2. Explore the Console

From the left navigation pane, review:

```text
Dashboards

Metrics

Alarms

Logs

Insights

Application Signals
```

For this workshop, focus on:

```text
Metrics

Logs

Dashboards
```

---

# Step 3. Review Lambda Metrics

Navigate to:

```text
Metrics → AWS/Lambda
```

Select:

```text
Backend API

AI Worker
```

Review:

```text
Invocations

Duration

Errors

Concurrent Executions

Throttles
```

---

# Step 4. Review API Gateway Metrics

Navigate to:

```text
Metrics → AWS/ApiGateway
```

Review:

```text
Count

Latency

4XXError

5XXError

IntegrationLatency
```

---

# Step 5. Review Amazon RDS Metrics

Navigate to:

```text
Metrics → AWS/RDS
```

Review:

```text
CPU Utilization

Database Connections

Free Storage Space

Read IOPS

Write IOPS
```

---

# Step 6. Review Amazon SQS Metrics

Navigate to:

```text
Metrics → AWS/SQS
```

Review:

```text
ApproximateNumberOfMessagesVisible

ApproximateAgeOfOldestMessage

NumberOfMessagesSent

NumberOfMessagesReceived
```

---

# Step 7. Review Amazon Athena Metrics

Navigate to:

```text
Metrics → AWS/Athena
```

Review:

```text
SuccessfulQueries

FailedQueries

QueryRuntime
```

---

# Step 8. Review Log Groups

Navigate to:

```text
Logs → Log Groups
```

Verify log groups such as:

```text
/aws/lambda/backend-api

/aws/lambda/ai-worker

/API-Gateway-Execution-Logs
```

Inspect the latest log stream and review timestamps, request IDs, execution details, and any error messages.

---

# Step 9. Review Lambda Logs

Open:

```text
/aws/lambda/ai-worker
```

Review a recent execution:

```text
START RequestId

Processing SQS Message

Running Athena Query

Generating AI Summary

END RequestId

REPORT Duration: 320 ms
```

---

# Step 10. Create a Monitoring Dashboard

Navigate to:

```text
Dashboards
```

Create a dashboard named:

```text
SupplyChainMonitoring
```

Add widgets for:

```text
Lambda Duration

Lambda Errors

API Gateway Latency

RDS CPU Utilization

SQS Queue Depth

Athena Query Count
```

---

## Best Practices

- Monitor metrics and logs regularly.
- Use consistent naming conventions.
- Build dashboards for critical services.
- Watch Duration, Errors, and Queue Depth closely.
- Grant CloudWatch permissions only to required IAM roles.

---

## Verification

Verify that:

- Metrics are available for Lambda, API Gateway, RDS, and SQS.
- Log groups exist for the Backend API and AI Worker.
- Recent log streams are accessible.
- The `SupplyChainMonitoring` dashboard has been created.
- Dashboard widgets display live metrics.

---

## Expected Outcome

After completing this section, you have:

- Learned how to use Amazon CloudWatch.
- Monitored AWS service metrics.
- Reviewed Lambda and API Gateway logs.
- Built a centralized monitoring dashboard.
- Prepared the environment for CloudWatch Alarms.
