---
title: "5.7.2 Configure CloudWatch Alarms"
date: 2024-01-01
weight: 2
chapter: false
---


## Overview

Amazon CloudWatch Alarms continuously monitor AWS service metrics and detect abnormal operating conditions.

When a metric crosses a defined threshold, the alarm changes to the **ALARM** state. In the next section, you will configure Amazon SNS to automatically notify administrators when alarms are triggered.

In this workshop, you will create alarms for:

- AWS Lambda
- Amazon API Gateway
- Amazon RDS
- Amazon SQS
- Amazon Athena

---

## Architecture

```text
AWS Services
      │
      ├── Lambda
      ├── API Gateway
      ├── Amazon RDS
      ├── Amazon SQS
      └── Amazon Athena
               │
               ▼
      Amazon CloudWatch
               │
       CloudWatch Alarms
               │
        Alarm State Change
               │
               ▼
      Amazon SNS (Next Section)
```

---

## Objectives

After completing this section, you will:

- Create CloudWatch alarms.
- Configure alarm thresholds.
- Monitor alarm states.
- Prepare for Amazon SNS integration.

---

# Step 1. Open CloudWatch Alarms

Open the AWS Management Console.

Navigate to:

```text
Amazon CloudWatch
```

Select:

```text
Alarms
```

Choose:

```text
Create alarm
```

---

# Step 2. Create a Lambda Error Alarm

Metric:

```text
AWS/Lambda

Errors
```

Lambda function:

```text
backend-api
```

Configuration:

| Property  | Value          |
| --------- | -------------- |
| Statistic | Sum            |
| Period    | 5 Minutes      |
| Threshold | Greater than 0 |

Alarm name:

```text
BackendLambdaErrors
```

---

# Step 3. Create a Lambda Duration Alarm

Metric:

```text
Duration
```

Configuration:

| Property  | Value   |
| --------- | ------- |
| Statistic | Average |
| Threshold | 5000 ms |

Alarm name:

```text
BackendLambdaDuration
```

---

# Step 4. Create an API Gateway Alarm

Metric:

```text
5XXError
```

Configuration:

| Property  | Value          |
| --------- | -------------- |
| Statistic | Sum            |
| Threshold | Greater than 0 |

Alarm name:

```text
ApiGateway5XXErrors
```

---

# Step 5. Create an Amazon RDS Alarm

Metric:

```text
CPUUtilization
```

Configuration:

| Property  | Value   |
| --------- | ------- |
| Statistic | Average |
| Threshold | 80%     |

Alarm name:

```text
RDSHighCPU
```

---

# Step 6. Create an Amazon SQS Alarm

Metric:

```text
ApproximateNumberOfMessagesVisible
```

Configuration:

| Property  | Value            |
| --------- | ---------------- |
| Statistic | Average          |
| Threshold | Greater than 100 |

Alarm name:

```text
SQSQueueDepth
```

---

# Step 7. Create an Amazon Athena Alarm

Metric:

```text
FailedQueries
```

Configuration:

| Property  | Value          |
| --------- | -------------- |
| Statistic | Sum            |
| Threshold | Greater than 0 |

Alarm name:

```text
AthenaFailedQueries
```

---

# Step 8. Review Alarm Status

Navigate to:

```text
CloudWatch → Alarms
```

Example:

| Alarm                 | State |
| --------------------- | ----- |
| BackendLambdaErrors   | OK    |
| BackendLambdaDuration | OK    |
| ApiGateway5XXErrors   | OK    |
| RDSHighCPU            | OK    |
| SQSQueueDepth         | OK    |
| AthenaFailedQueries   | OK    |

When a threshold is exceeded, the state changes to:

```text
ALARM
```

---

# Step 9. Review Alarm History

Open any alarm and review the **History** tab to see:

- Alarm creation time.
- State transitions.
- Metric values when the alarm was triggered.

---

## Best Practices

- Use clear and consistent alarm names.
- Set thresholds based on application behavior.
- Review alarm history regularly.
- Create alarms only for meaningful metrics.
- Integrate alarms with Amazon SNS for automated notifications.

---

## Verification

Verify that:

- All alarms are created successfully.
- Each alarm is in the **OK** state.
- Alarm metrics and history are accessible.
- The environment is ready for Amazon SNS integration.

---

## Expected Outcome

After completing this section, you have:

- Configured CloudWatch Alarms for critical AWS services.
- Defined operational thresholds.
- Learned how to monitor alarm states and history.
- Prepared the monitoring system for automated notifications using Amazon SNS.
