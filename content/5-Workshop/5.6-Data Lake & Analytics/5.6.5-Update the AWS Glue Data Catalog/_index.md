---
title: "5.6.5 Update the AWS Glue Data Catalog"
date: 2024-01-01
weight: 5
chapter: false
---

# 5.6.5 Update the AWS Glue Data Catalog

## Overview

After converting the datasets to Apache Parquet and organizing them into partitions, the next step is to update the **AWS Glue Data Catalog**.

The Glue Data Catalog stores metadata such as:

- Databases
- Tables
- Columns
- Data types
- Partitions
- Amazon S3 locations

Amazon Athena uses this metadata to execute SQL queries without directly interpreting the physical layout of data stored in Amazon S3.

---

## Architecture

```text
Amazon S3 Processed Zone
          │
          ▼
 AWS Glue Data Catalog
          │
          ▼
 Amazon Athena
          │
    ┌─────┴─────┐
    ▼           ▼
 Dashboard   AI Worker
```

---

## Objectives

After completing this section, you will:

- Verify the Glue database.
- Review the registered tables.
- Validate table schemas.
- Verify partition metadata.
- Synchronize metadata with Amazon S3.
- Prepare datasets for analytics and AI workloads.

---

# Step 1. Open the AWS Glue Console

Sign in to the AWS Management Console.

Search for:

```text
AWS Glue
```

Select:

```text
Data Catalog
```

---

# Step 2. Verify the Database

Open:

```text
Databases
```

Verify that the following database exists:

```text
supplychain_catalog
```

---

# Step 3. Verify the Tables

Open the database:

```text
supplychain_catalog
```

Verify the following tables:

```text
inventory
orders
shipments
suppliers

inventory_parquet
orders_parquet
shipments_parquet
suppliers_parquet

inventory_partitioned
orders_partitioned
shipments_partitioned
```

---

# Step 4. Review the Schema

For example:

```text
orders_partitioned
```

Verify the columns:

```text
order_id

customer_id

product_id

warehouse_id

quantity

unit_price

order_status

order_date

year

month
```

Confirm that the column data types are appropriate.

Examples:

```text
varchar

integer

double

date
```

---

# Step 5. Verify the Partitions

Open:

```text
orders_partitioned
```

Select:

```text
Partitions
```

Example:

```text
year=2026/month=07
```

For Inventory:

```text
warehouse_id=WH001/year=2026/month=07
```

---

# Step 6. Refresh Partition Metadata

When new partition folders are added directly to Amazon S3, refresh the metadata.

Example:

```text
year=2026/month=08/
```

Run:

```sql
MSCK REPAIR TABLE orders_partitioned;
```

Or manually register the partition:

```sql
ALTER TABLE orders_partitioned
ADD IF NOT EXISTS
PARTITION (
year='2026',
month='08'
)
LOCATION
's3://ai-supplychain-datalake/processed/orders-partitioned/year=2026/month=08/';
```

---

# Step 7. Verify Using Athena

Run:

```sql
SHOW TABLES;
```

Verify partitions:

```sql
SHOW PARTITIONS orders_partitioned;
```

---

# Step 8. Verify the Storage Location

Example:

```text
orders_partitioned
```

Location:

```text
s3://ai-supplychain-datalake/processed/orders-partitioned/
```

Verify that the table points to the correct Amazon S3 prefix.

---

## Best Practices

- Avoid manually modifying schemas unless necessary.
- Use AWS Glue Crawlers when managing multiple datasets.
- Keep partition metadata synchronized.
- Validate data types before running analytics queries.
- Use consistent naming conventions for databases and tables.

---

## Verification

Verify that:

- The database exists.
- All expected tables are registered.
- Schemas are correct.
- Partition metadata is available.
- Athena queries execute successfully.

---

## Expected Outcome

After completing this section, you have:

- Updated the AWS Glue Data Catalog.
- Synchronized metadata with Amazon S3.
- Prepared datasets for Amazon Athena.
- Completed the metadata layer of the Data Lake.
