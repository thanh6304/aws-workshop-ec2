---
title: "Workshop"
date: 2026-06-07
weight: 5
chapter: false
pre: " <b> 5. </b> "
---


# Deploying an AI Supply Chain Control Tower on AWS

### Access Link: http://52.202.59.216/login

#### Overview

**AI Supply Chain Control Tower** is a cloud-native platform designed to help organizations monitor, analyze, and optimize supply chain operations using AWS managed services and Artificial Intelligence. The system provides centralized data management, real-time monitoring, demand forecasting, supplier risk analysis, and intelligent recommendations for decision making.

In this workshop, you will learn how to deploy a complete AI-powered Supply Chain Control Tower on AWS using a serverless architecture. The workshop covers infrastructure deployment, backend development, data processing, AI integration, monitoring, and security best practices.

The architecture leverages the following AWS services:

- **Amazon Route 53** – Domain Name System (DNS) management.
- **AWS Amplify** – Hosting and deploying the frontend web application.
- **Amazon Cognito** – User authentication and authorization.
- **Amazon API Gateway** – REST API management.
- **AWS Lambda** – Backend business logic and AI processing.
- **Amazon RDS for PostgreSQL** – Relational database for transactional data.
- **Amazon SQS** – Asynchronous message processing.
- **Amazon S3** – Centralized Data Lake storage.
- **AWS Glue** – Metadata catalog and ETL services.
- **Amazon Athena** – Serverless SQL analytics on data stored in Amazon S3.
- **Amazon Bedrock** – Foundation models for AI-powered forecasting and recommendations.
- **Amazon CloudWatch** – Monitoring, logging, and metrics collection.
- **Amazon SNS** – Notification and alert delivery.
- **AWS IAM** – Identity and access management.
- **AWS Secrets Manager** – Secure credential management.
- **AWS KMS** – Data encryption and key management.
- **AWS CloudTrail** – Auditing and governance.
- **AWS CloudFormation** – Infrastructure as Code (IaC) deployment.

#### Content

1. [Introduction](5.1-Introduction/)
2. [Environment Preparation](5.2-Prerequisite/)
3. [Deploy AWS Infrastructure](5.3-Infrastructure/)
4. [Deploy Backend API](5.4-Backend/)
5. [Deploy AI Pipeline](5.5-AI-Pipeline/)
6. [Deploy Data Lake & Analytics](5.6-Data-Lake/)
7. [Monitoring & Security](5.7-Monitoring-Security/)
8. [System Testing](5.8-Testing/)
9. [Resource Cleanup](5.9-Cleanup/)
