---
title: "5.6.1 Organize the Amazon S3 Data Lake"
date: 2024-01-01
weight: 1
chapter: false
---


## Overview

Amazon S3 serves as the centralized storage layer for the **AI Supply Chain Control Tower**.

Instead of storing all datasets in a single location, the Data Lake is organized into multiple logical zones based on the data lifecycle and usage.

In this workshop, the Data Lake consists of three primary zones:

```text
Raw Zone
Processed Zone
Analytics Zone
```

A dedicated folder is also used to store Amazon Athena query results.

---

## Architecture

```text
Amazon S3 Data Lake
│
├── raw/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── processed/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── analytics/
│   ├── inventory-summary/
│   ├── order-summary/
│   ├── shipment-summary/
│   └── supplier-performance/
│
└── athena-results/
```

---

## Objectives

After completing this section, you will:

- Understand the purpose of each Data Lake zone.
- Create a logical folder structure in Amazon S3.
- Separate raw and processed datasets.
- Prepare a location for Amazon Athena query results.
- Configure basic security settings for the S3 bucket.

---

## Data Lake Zones

### Raw Zone

The Raw Zone stores original datasets uploaded from operational systems.

Examples include:

```text
CSV files
JSON files
Application exports
Supplier datasets
Warehouse reports
```

Raw data should remain unchanged after ingestion.

Example locations:

```text
s3://ai-supplychain-datalake/raw/inventory/
s3://ai-supplychain-datalake/raw/orders/
s3://ai-supplychain-datalake/raw/shipments/
s3://ai-supplychain-datalake/raw/suppliers/
```

---

### Processed Zone

The Processed Zone stores cleaned and optimized datasets.

Typical processing includes:

- Data cleansing
- Data validation
- Type conversion
- Duplicate removal
- Apache Parquet conversion
- Data partitioning

Example:

```text
s3://ai-supplychain-datalake/processed/orders/
```

---

### Analytics Zone

The Analytics Zone stores aggregated datasets used by:

- Dashboards
- Business reports
- AI analysis
- KPI monitoring
- Frequently executed analytics queries

Example:

```text
s3://ai-supplychain-datalake/analytics/inventory-summary/
```

---

### Athena Results

Amazon Athena stores query results in a dedicated folder.

Example:

```text
s3://ai-supplychain-datalake/athena-results/
```

This location should contain only Athena query output rather than business datasets.

---

# Implementation

## Step 1. Open the Amazon S3 Console

Sign in to the AWS Management Console.

Search for:

```text
Amazon S3
```

Open the bucket:

```text
ai-supplychain-datalake
```

This bucket was created during the infrastructure deployment.

---

## Step 2. Create the Raw Zone

Create a folder named:

```text
raw
```

Inside the folder, create:

```text
inventory
orders
shipments
suppliers
```

Result:

```text
raw/
├── inventory/
├── orders/
├── shipments/
└── suppliers/
```

---

## Step 3. Create the Processed Zone

Create a folder named:

```text
processed
```

Inside the folder create:

```text
inventory
orders
shipments
suppliers
```

Result:

```text
processed/
├── inventory/
├── orders/
├── shipments/
└── suppliers/
```

---

## Step 4. Create the Analytics Zone

Create a folder named:

```text
analytics
```

Create the following subfolders:

```text
inventory-summary
order-summary
shipment-summary
supplier-performance
```

Result:

```text
analytics/
├── inventory-summary/
├── order-summary/
├── shipment-summary/
└── supplier-performance/
```

---

## Step 5. Create the Athena Results Folder

Create a folder named:

```text
athena-results
```

Full path:

```text
s3://ai-supplychain-datalake/athena-results/
```

This location will later be configured as the Amazon Athena query result output.

---

## Step 6. Enable Bucket Versioning

Open:

```text
Properties
```

Locate:

```text
Bucket Versioning
```

Choose:

```text
Edit
```

Then enable:

```text
Enable
```

Versioning protects objects from accidental overwrite or deletion.

---

## Step 7. Verify Block Public Access

Open:

```text
Permissions
```

Verify:

```text
Block all public access
```

Status should be:

```text
Enabled
```

Supply chain data should never be publicly accessible.

---

## Step 8. Configure Default Encryption

Open:

```text
Properties
```

Locate:

```text
Default encryption
```

Recommended options:

```text
SSE-S3
```

or

```text
SSE-KMS
```

AWS KMS is recommended for production environments because it provides fine-grained key management and access control.

---

## Step 9. Verify the Data Lake Structure

The completed bucket structure should look like:

```text
ai-supplychain-datalake/
│
├── raw/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── processed/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── analytics/
│   ├── inventory-summary/
│   ├── order-summary/
│   ├── shipment-summary/
│   └── supplier-performance/
│
└── athena-results/
```

---

## Recommended Naming Convention

Use the following naming pattern:

```text
<dataset>_<date>_<sequence>.<extension>
```

Examples:

```text
inventory_20260722_001.csv
orders_20260722_001.csv
shipments_20260722_001.csv
suppliers_20260722_001.csv
```

Avoid:

- Spaces
- Special characters
- Generic filenames such as:

```text
data.csv
new.csv
final-final.csv
```

---

## Recommended Partition Structure

Processed datasets can be organized using partitions such as:

```text
processed/orders/
└── year=2026/
    └── month=07/
        └── day=22/
            └── orders.parquet
```

Inventory data can also be partitioned by warehouse:

```text
processed/inventory/
└── warehouse_id=WH001/
    └── year=2026/
        └── month=07/
            └── inventory.parquet
```

Partitioning allows Amazon Athena to scan only the required partitions, improving query performance and reducing query costs.

---

## Best Practices

- Never modify files in the Raw Zone.
- Keep the S3 bucket private.
- Enable default encryption.
- Enable bucket versioning.
- Follow a consistent naming convention.
- Separate raw and processed datasets.
- Avoid temporary files in the Analytics Zone.
- Minimize the number of small files.
- Partition datasets using frequently filtered columns.
- Configure Lifecycle Rules for long-term storage management.

---

## Verification

Verify that:

- The S3 bucket is not publicly accessible.
- The Raw Zone has been created.
- The Processed Zone has been created.
- The Analytics Zone has been created.
- The Athena Results folder exists.
- Versioning is enabled.
- Default encryption is configured.
- Folder names follow a consistent naming convention.

---

## Expected Outcome

After completing this section, you have:

- Designed an Amazon S3 Data Lake structure.
- Organized datasets into Raw, Processed, and Analytics zones.
- Prepared a dedicated location for Amazon Athena query results.
- Applied basic storage security configurations.
- Built the foundation for supply chain analytics and AI workloads.
