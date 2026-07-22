---
title: "5.6 Data Lake and Analytics"
date: 2024-01-01
weight: 6
chapter: false
---

## Overview

In this chapter, you will complete the Data Lake and Analytics layer of the **AI Supply Chain Control Tower**.

Inventory, order, shipment, supplier, and reporting data will be stored centrally in Amazon S3. AWS Glue Data Catalog will manage the dataset metadata, while Amazon Athena will provide serverless SQL queries directly against the data stored in Amazon S3.

The datasets will also be optimized using Apache Parquet and partitioning. This enables Athena to read only the required columns and partitions, improving query performance and reducing the amount of data scanned.

---

## Architecture

```text
Operational Systems
        │
        ▼
Amazon S3 Data Lake
        │
        ├── Raw Zone
        ├── Processed Zone
        └── Analytics Zone
                │
                ▼
       AWS Glue Data Catalog
                │
                ▼
          Amazon Athena
                │
        ┌───────┴────────┐
        ▼                ▼
 AI Worker Lambda     Dashboard
        │
        ▼
 Amazon Bedrock
```

---

## Objectives

After completing this chapter, you will:

- Organize supply chain data in an Amazon S3 Data Lake.
- Separate data into Raw, Processed, and Analytics zones.
- Convert CSV datasets to Apache Parquet.
- Partition data by date or warehouse.
- Update metadata in AWS Glue Data Catalog.
- Configure an Amazon Athena Workgroup.
- Build supply chain analytics queries.
- Create reusable views for the Dashboard.
- Verify query performance and scanned data volume.

---

## Chapter Contents

### 5.6.1 Organize the Amazon S3 Data Lake

Design the bucket structure and create the following zones:

```text
raw/
processed/
analytics/
athena-results/
```

### 5.6.2 Upload Supply Chain Data

Prepare and upload the following datasets:

```text
inventory
orders
shipments
suppliers
```

### 5.6.3 Convert Data to Apache Parquet

Use Athena CTAS queries to convert CSV datasets into the Apache Parquet columnar format.

### 5.6.4 Partition the Data

Organize datasets using partition keys such as:

```text
year
month
day
warehouse_id
```

### 5.6.5 Update the AWS Glue Data Catalog

Update schemas, tables, and partitions so Athena can query the optimized datasets.

### 5.6.6 Create an Athena Workgroup

Create a dedicated workgroup, configure the query result location, and apply data scan controls.

### 5.6.7 Build Analytics Queries

Create queries for:

- Low inventory.
- Order status.
- Revenue trends.
- Delayed shipments.
- Supplier performance.
- Demand trends.

### 5.6.8 Create Dashboard Views

Create reusable Athena views for the Backend API and application Dashboard.

### 5.6.9 Test the Data Lake and Analytics Layer

Validate the S3 datasets, Glue metadata, Athena queries, partitions, and Dashboard results.

---

## Data Processing Flow

```text
CSV / JSON Data
       │
       ▼
Amazon S3 Raw Zone
       │
       ▼
Athena CTAS
       │
       ▼
Amazon S3 Processed Zone
       │
       ▼
AWS Glue Data Catalog
       │
       ▼
Amazon Athena
       │
       ├── Dashboard Queries
       └── AI Analysis Queries
```

---

## Expected Outcome

After completing this chapter:

- Supply chain data is centrally stored in Amazon S3.
- The Data Lake is organized into clearly defined zones.
- Analytics datasets are stored in Apache Parquet.
- Tables are partitioned to reduce scanned data.
- AWS Glue Data Catalog manages the metadata.
- Amazon Athena can query the datasets using SQL.
- The Dashboard can consume reusable analytics views.
- The AI Worker Lambda can retrieve aggregated data for Amazon Bedrock.
