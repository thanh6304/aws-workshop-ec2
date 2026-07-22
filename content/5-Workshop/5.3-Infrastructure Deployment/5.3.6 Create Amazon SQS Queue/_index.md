---
title: "5.3.6 Create Amazon SQS Queue"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.3.6 Create Amazon SQS Queue

## Overview

Amazon Simple Queue Service (Amazon SQS) is a fully managed message queuing service that enables asynchronous communication between distributed application components.

In the **AI Supply Chain Control Tower**, Amazon SQS acts as the communication layer between the Backend API and the AI Processing Pipeline.

Instead of processing AI requests synchronously, the Lambda API publishes a message to Amazon SQS. A separate Lambda Worker then consumes the message and performs analytics using Amazon Athena and Amazon Bedrock.

This architecture provides:

- Asynchronous processing
- Better API responsiveness
- Improved scalability
- Higher fault tolerance
- Reliable message delivery

---

# Architecture

```
User
 │
 ▼
API Gateway
 │
 ▼
Lambda API
 │
 │ Send Message
 ▼
Amazon SQS
 │
 │ Trigger
 ▼
Lambda Worker
 │
 ▼
Amazon Athena
 │
 ▼
Amazon Bedrock
 │
 ▼
Amazon RDS
```

---

# Objectives

After completing this section, you will be able to:

- Create a Standard Queue.
- Configure Visibility Timeout.
- Configure Message Retention.
- Create a Dead-Letter Queue (DLQ).
- Verify queue functionality.

---

# Implementation Steps

### Step 1. Open Amazon SQS

Navigate to **Amazon SQS** from the AWS Management Console.
![Kiến trúc Amazon SQS](/images/5-Workshop/5.3-S3-vpc/5.3.6.png)

### Step 2. Create a Queue

Choose **Create Queue**.

### Step 3. Select Queue Type

Select **Standard Queue**.

### Step 4. Configure the Queue

| Property             | Value               |
| -------------------- | ------------------- |
| Queue Name           | ai-processing-queue |
| Queue Type           | Standard            |
| Visibility Timeout   | 60 Seconds          |
| Message Retention    | 4 Days              |
| Delivery Delay       | 0 Seconds           |
| Maximum Message Size | 256 KB              |

### Step 5. Create a Dead-Letter Queue

Create a second queue named:

```
ai-processing-dlq
```

Configure the primary queue to use the DLQ with a maximum receive count of **5**.

### Step 6. Create the Queue

Click **Create Queue**.

### Step 7. Verify the Queue

Confirm that the following queues are available:

- ai-processing-queue
- ai-processing-dlq

---

# Best Practices

- Use Standard Queues for high-throughput AI workloads.
- Configure a Dead-Letter Queue to isolate failed messages.
- Store large datasets in Amazon S3 instead of embedding them in queue messages.
- Combine Amazon SQS with AWS Lambda for scalable event-driven architectures.

---

# Expected Outcome

After completing this section, you have successfully deployed an Amazon SQS Standard Queue and a Dead-Letter Queue, providing a reliable asynchronous messaging layer for the AI processing workflow.
