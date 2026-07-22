---
title: "5.3.5 Create Amazon S3 Data Lake"
date: 2024-01-01
weight: 5
chapter: false
---

# 5.3.5 Create Amazon S3 Data Lake

## Overview

Amazon Simple Storage Service (Amazon S3) is AWS's highly scalable object storage service.

In the **AI Supply Chain Control Tower**, Amazon S3 serves as the **Data Lake**, storing structured and semi-structured business data for analytics and AI workloads.

Amazon Athena will query data stored in S3 through AWS Glue Data Catalog, while Amazon Bedrock will use the query results to generate intelligent recommendations.

---

## Architecture

```
Business Data
        │
        ▼
Amazon S3 Data Lake
        │
        ├────────► AWS Glue Data Catalog
        │
        ▼
Amazon Athena
        │
        ▼
Lambda Worker
        │
        ▼
Amazon Bedrock
```

---

## Objectives

After completing this section, you will:

- Create an Amazon S3 bucket.
- Enable Block Public Access.
- Enable Versioning.
- Create a logical folder structure.
- Upload sample datasets.

---

## Implementation Steps

### Step 1. Open Amazon S3

Navigate to the Amazon S3 Console.

![Kiến trúc S3](/images/5-Workshop/5.3-S3-vpc/5.3.5.png)
### Step 2. Create a Bucket

Click:

```
Create bucket
```

### Step 3. Configure the Bucket

| Property | Value |
|----------|-------|
| Bucket Name | ai-supplychain-datalake |
| Region | ap-southeast-1 |
| Object Ownership | ACLs disabled |
| Block Public Access | Enabled |

### Step 4. Enable Versioning

Enable **Bucket Versioning** to protect against accidental deletion and overwrites.

### Step 5. Create Folder Structure

Create the following folders:

```
inventory/
orders/
shipments/
reports/
analytics/
ai-results/
logs/
```

### Step 6. Upload Sample Data

Upload sample CSV or JSON files such as:

```
inventory.csv
orders.csv
shipment.csv
```

These datasets will be queried later using Amazon Athena.

### Step 7. Verify the Bucket

Confirm that all folders and uploaded objects appear correctly in the bucket.

---

## Best Practices

- Keep **Block Public Access** enabled.
- Organize objects using prefixes (folders).
- Enable Versioning.
- Encrypt sensitive data using AWS KMS.
- Follow the principle of least privilege when granting bucket access.

---

## Expected Outcome

After completing this section, you have successfully created an Amazon S3 Data Lake, uploaded sample datasets, and prepared the storage layer for AWS Glue, Amazon Athena, and Amazon Bedrock.