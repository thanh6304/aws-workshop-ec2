---
title: "AI Pipeline Deployment"
date: 2024-01-01
weight: 5
chapter: false
pre: "<b>5.5. </b>"
---

## Overview

This chapter introduces the AI processing workflow of the AI Supply Chain Control Tower.

The AI pipeline combines asynchronous messaging, serverless computing, data analytics, and generative AI services to process business requests efficiently.

The workflow consists of:

- Backend Lambda publishes requests to Amazon SQS.
- Lambda Worker consumes queue messages.
- Amazon Athena queries datasets stored in Amazon S3.
- Amazon Bedrock generates AI insights.
- Lambda Worker stores AI results in Amazon RDS PostgreSQL.

---

## Objectives

After completing this chapter, you will be able to:

- Create an AWS Glue Data Catalog.
- Query datasets with Amazon Athena.
- Deploy an AI Worker Lambda.
- Integrate Amazon Bedrock.
- Process Amazon SQS messages.
- Store AI analysis results.
- Validate the end-to-end AI pipeline.

---

## Architecture

```
Amazon SQS
      │
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
Amazon RDS PostgreSQL
```

---

## Contents

- 5.5.1 Create AWS Glue Data Catalog
- 5.5.2 Query Data with Amazon Athena
- 5.5.3 Create AI Worker Lambda
- 5.5.4 Integrate Amazon Bedrock
- 5.5.5 Process Messages from Amazon SQS
- 5.5.6 Store AI Analysis Results
- 5.5.7 Test the AI Pipeline

---

## Expected Outcome

After completing this chapter, the AI processing workflow will automatically analyze business data and generate intelligent recommendations using Amazon Bedrock.
