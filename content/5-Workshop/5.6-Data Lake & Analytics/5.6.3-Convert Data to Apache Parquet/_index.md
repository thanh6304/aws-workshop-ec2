---
title: "5.6.3 Convert Data to Apache Parquet"
date: 2024-01-01
weight: 3
chapter: false
---


## Overview

The datasets uploaded to Amazon S3 are currently stored in CSV format.

While CSV files are easy to create and exchange, they are not optimized for analytical workloads.

Amazon Athena performs significantly better with **Apache Parquet**, a columnar storage format that reads only the required columns instead of the entire file.

In this section, you will use **Athena CREATE TABLE AS SELECT (CTAS)** statements to convert the CSV datasets into Apache Parquet.

---

## Architecture

```text
Amazon S3 Raw Zone (CSV)
        │
        ▼
Amazon Athena (CTAS)
        │
        ▼
Amazon S3 Processed Zone (Parquet)
        │
        ▼
AWS Glue Data Catalog
        │
        ▼
Dashboard / AI Worker Lambda
```

---

## Objectives

After completing this section, you will:

- Understand the advantages of Apache Parquet.
- Convert CSV datasets into Parquet.
- Store optimized datasets in the Processed Zone.
- Verify the generated Parquet files.
- Prepare optimized datasets for Amazon Athena.

---

## Why Apache Parquet?

Apache Parquet is a columnar storage format.

Benefits include:

- Reduced Athena data scanning.
- Faster query execution.
- Built-in compression.
- Column-level reading.
- Lower query costs.

Example:

CSV

```text
Athena reads the entire file.
```

Parquet

```text
Athena reads only the required columns.
```

---

# Step 1. Open Amazon Athena

Sign in to the AWS Management Console.

Open:

```text
Amazon Athena
```

Select the database:

```text
supplychain_catalog
```

Verify that the CSV tables have already been created by the AWS Glue Crawler.

---

# Step 2. Convert the Inventory Table

Run:

```sql
CREATE TABLE inventory_parquet
WITH (
    format = 'PARQUET',
    external_location = 's3://ai-supplychain-datalake/processed/inventory/'
)
AS
SELECT *
FROM inventory;
```

Athena will:

- Read the CSV dataset.
- Convert it to Apache Parquet.
- Store the output in:

```text
processed/inventory/
```

---

# Step 3. Convert the Orders Table

```sql
CREATE TABLE orders_parquet
WITH (
    format = 'PARQUET',
    external_location = 's3://ai-supplychain-datalake/processed/orders/'
)
AS
SELECT *
FROM orders;
```

---

# Step 4. Convert the Shipments Table

```sql
CREATE TABLE shipments_parquet
WITH (
    format = 'PARQUET',
    external_location = 's3://ai-supplychain-datalake/processed/shipments/'
)
AS
SELECT *
FROM shipments;
```

---

# Step 5. Convert the Suppliers Table

```sql
CREATE TABLE suppliers_parquet
WITH (
    format = 'PARQUET',
    external_location = 's3://ai-supplychain-datalake/processed/suppliers/'
)
AS
SELECT *
FROM suppliers;
```

---

# Step 6. Verify the Generated Files

Open the Amazon S3 Console.

Navigate to:

```text
processed/
```

Verify that each dataset contains:

```text
.parquet
```

files.

---

# Step 7. Verify the Tables

Run:

```sql
SELECT *
FROM inventory_parquet
LIMIT 10;
```

If the query succeeds, Athena is reading the Parquet dataset correctly.

---

# Step 8. Compare Query Performance

Execute:

```sql
SELECT product_name
FROM inventory;
```

Then execute:

```sql
SELECT product_name
FROM inventory_parquet;
```

Compare the:

```text
Data scanned
```

metric.

The Parquet table should scan significantly less data than the CSV table.

---

## Verification

Verify that:

- Four Parquet tables have been created.
- Parquet files exist in the Processed Zone.
- Athena successfully queries the Parquet datasets.
- The scanned data size is lower than when querying CSV.

---

## Best Practices

- Avoid querying large CSV datasets directly.
- Use Apache Parquet for analytics workloads.
- Combine Parquet with table partitioning.
- Keep CSV datasets in the Raw Zone.
- Store optimized Parquet datasets only in the Processed Zone.

---

## Expected Outcome

After completing this section, you have:

- Converted CSV datasets into Apache Parquet.
- Optimized datasets for Amazon Athena.
- Prepared the data for the Dashboard and AI Worker Lambda.
