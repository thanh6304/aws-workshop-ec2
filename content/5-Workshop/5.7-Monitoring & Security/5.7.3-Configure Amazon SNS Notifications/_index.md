---
title: "5.7.3 Configure Amazon SNS Notifications"
date: 2024-01-01
weight: 3
chapter: false
---

# 5.7.3 Configure Amazon SNS Notifications

## Overview

In the previous section, you created CloudWatch Alarms to monitor your AWS resources. However, administrators should not have to manually check the AWS Console for alarm status.

Amazon Simple Notification Service (Amazon SNS) enables CloudWatch to automatically send notifications whenever an alarm changes state.

In this workshop, Amazon SNS will notify administrators when:

- AWS Lambda encounters errors.
- Amazon API Gateway returns 5XX errors.
- Amazon RDS CPU utilization exceeds the defined threshold.
- Amazon SQS queue depth becomes too high.
- Amazon Athena query execution fails.

---

## Architecture

```text
AWS Services
      │
      ▼
CloudWatch Metrics
      │
      ▼
CloudWatch Alarms
      │
      ▼
Amazon SNS Topic
      │
      ▼
Email Notification
      │
      ▼
Administrator
```

---

## Objectives

After completing this section, you will:

- Create an Amazon SNS topic.
- Subscribe an email endpoint.
- Connect CloudWatch Alarms to the SNS topic.
- Verify email notifications.

---

# Step 1. Open Amazon SNS

From the AWS Management Console, search for:

```text
Amazon SNS
```

Open:

```text
Amazon Simple Notification Service
```

---

# Step 2. Create an SNS Topic

Navigate to:

```text
Topics
```

Choose:

```text
Create topic
```

Select:

```text
Standard
```

Topic name:

```text
SupplyChainAlerts
```

Description:

```text
Notification topic for AI Supply Chain Control Tower
```

Create the topic.

---

# Step 3. Create a Subscription

Open the topic and choose:

```text
Create subscription
```

Configuration:

| Property | Value                  |
| -------- | ---------------------- |
| Protocol | Email                  |
| Endpoint | your-email@example.com |

Create the subscription.

---

# Step 4. Confirm the Subscription

Amazon SNS sends a confirmation email.

Open the email and select:

```text
Confirm subscription
```

The subscription status should become:

```text
Confirmed
```

---

# Step 5. Attach SNS to CloudWatch Alarms

Navigate to:

```text
CloudWatch → Alarms
```

Edit each alarm and configure the notification action to use the `SupplyChainAlerts` topic.

Repeat this process for:

```text
BackendLambdaErrors

BackendLambdaDuration

ApiGateway5XXErrors

RDSHighCPU

SQSQueueDepth

AthenaFailedQueries
```

---

# Step 6. Review Alarm Actions

When an alarm changes to:

```text
ALARM
```

CloudWatch publishes a message to:

```text
Amazon SNS
```

Amazon SNS then sends an email notification to subscribed administrators.

---

# Step 7. Verify Email Notifications

Example notification:

```text
Subject

ALARM:
BackendLambdaErrors

Message

Alarm Name:
BackendLambdaErrors

State:
ALARM

Reason:
Threshold Crossed

Metric:
Errors
```

---

# Step 8. Review SNS Metrics

Navigate to:

```text
Metrics
```

Review:

```text
NumberOfMessagesPublished

NumberOfNotificationsDelivered

NumberOfNotificationsFailed
```

These metrics confirm whether notifications were successfully delivered.

---

## Best Practices

- Use a dedicated SNS topic for operational alerts.
- Confirm subscriptions immediately after creation.
- Use descriptive topic names.
- Subscribe only authorized recipients.
- Integrate CloudWatch Alarms with Amazon SNS for automated incident notification.

---

## Verification

Verify that:

- The `SupplyChainAlerts` topic exists.
- The email subscription status is **Confirmed**.
- All CloudWatch Alarms are configured to publish to the SNS topic.
- Email notifications are received when alarms enter the **ALARM** state.
- SNS metrics show successful message delivery.

---

## Expected Outcome

After completing this section, you have:

- Created an Amazon SNS topic.
- Confirmed an email subscription.
- Connected CloudWatch Alarms to Amazon SNS.
- Implemented automated operational notifications for the AI Supply Chain Control Tower.
