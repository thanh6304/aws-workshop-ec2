---
title: "5.6.4 Partition Data for Amazon Athena"
date: 2024-01-01
weight: 4
chapter: false
---


## Overview

After converting the CSV datasets to Apache Parquet, the next step is to organize the data into partitions.

Partitioning separates a dataset into different Amazon S3 folders based on one or more fields, such as:

```text
year
month
day
warehouse_id
```

When a query filters by a partition key, Amazon Athena can read only the matching partitions instead of scanning the complete dataset.

For example:

```sql
WHERE year = '2026'
  AND month = '07'
```

limits the query to the corresponding year and month partition.

In this section, you will use Athena CTAS statements to create partitioned Parquet tables.

---

## Architecture

```text
Amazon S3 Raw Zone
      │
      │ CSV
      ▼
Amazon Athena CTAS
      │
      │ Parquet + Partition
      ▼
Amazon S3 Processed Zone
      │
      ├── inventory-partitioned/
      │   └── warehouse_id=WH001/
      │       └── year=2026/
      │           └── month=07/
      │
      ├── orders-partitioned/
      │   └── year=2026/
      │       └── month=07/
      │
      └── shipments-partitioned/
          └── year=2026/
              └── month=07/
                     │
                     ▼
          AWS Glue Data Catalog
                     │
                     ▼
               Amazon Athena
```

---

## Objectives

After completing this section, you will:

- Understand how partitioning organizes data in Amazon S3.
- Select suitable partition keys for each dataset.
- Create partitioned Parquet tables with Athena CTAS.
- Verify partition folders in Amazon S3.
- Query datasets using partition filters.
- Compare the amount of data scanned.
- Understand how partition metadata is updated.

---

## Recommended Partition Design

### Inventory

Inventory data is commonly analyzed by warehouse and update period.

Use:

```text
warehouse_id
year
month
```

Amazon S3 structure:

```text
processed/inventory-partitioned/
└── warehouse_id=WH001/
    └── year=2026/
        └── month=07/
            └── data.parquet
```

### Orders

Order data is commonly analyzed by time period.

Use:

```text
year
month
```

Structure:

```text
processed/orders-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

### Shipments

Shipment data is commonly queried by expected delivery period and shipment status.

For this workshop, use:

```text
year
month
```

Structure:

```text
processed/shipments-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

### Suppliers

The supplier dataset is small and changes less frequently. Therefore, the `suppliers_parquet` table will remain unpartitioned in this workshop.

---

## Before You Begin

The `external_location` used by an Athena CTAS statement must not contain existing data.

Do not reuse:

```text
processed/inventory/
processed/orders/
processed/shipments/
```

Create new locations instead:

```text
processed/inventory-partitioned/
processed/orders-partitioned/
processed/shipments-partitioned/
```

If you previously executed the CTAS statements, remove the test table and existing output objects before running them again.

---

# Step 1. Open Amazon Athena

Sign in to the AWS Management Console.

Open:

```text
Amazon Athena
```

In the Query Editor, select:

```text
Data source: AwsDataCatalog
```

Select the database:

```text
supplychain_catalog
```

---

# Step 2. Verify the Date Column Types

Run:

```sql
DESCRIBE inventory;
```

Then run:

```sql
DESCRIBE orders;
```

```sql
DESCRIBE shipments;
```

If AWS Glue detected the date columns as `string`, extract the year and month with `substr`.

Example:

```sql
substr(order_date, 1, 4)
substr(order_date, 6, 2)
```

If a date column already uses the `date` type, you can use:

```sql
CAST(year(order_date) AS varchar)
CAST(month(order_date) AS varchar)
```

The workshop queries assume that the date columns currently use the following type:

```text
string
```

---

# Step 3. Create the Partitioned Inventory Table

Run:

```sql
CREATE TABLE inventory_partitioned
WITH (
    format = 'PARQUET',
    parquet_compression = 'SNAPPY',
    external_location = 's3://ai-supplychain-datalake/processed/inventory-partitioned/',
    partitioned_by = ARRAY['warehouse_id', 'year', 'month']
)
AS
SELECT
    inventory_id,
    product_id,
    product_name,
    quantity,
    reorder_level,
    unit_cost,
    last_updated,
    warehouse_id,
    substr(last_updated, 1, 4) AS year,
    substr(last_updated, 6, 2) AS month
FROM inventory;
```

The partition columns must appear at the end of the CTAS `SELECT` column list.

Expected structure:

```text
processed/inventory-partitioned/
├── warehouse_id=WH001/
│   └── year=2026/
│       └── month=07/
│
├── warehouse_id=WH002/
│   └── year=2026/
│       └── month=07/
│
└── warehouse_id=WH003/
    └── year=2026/
        └── month=07/
```

---

# Step 4. Verify the Inventory Table

Run:

```sql
SELECT *
FROM inventory_partitioned
LIMIT 10;
```

Filter by warehouse:

```sql
SELECT
    product_id,
    product_name,
    quantity,
    reorder_level
FROM inventory_partitioned
WHERE warehouse_id = 'WH001'
  AND year = '2026'
  AND month = '07';
```

Find low-stock products:

```sql
SELECT
    warehouse_id,
    product_id,
    product_name,
    quantity,
    reorder_level
FROM inventory_partitioned
WHERE year = '2026'
  AND month = '07'
  AND quantity < reorder_level
ORDER BY warehouse_id, quantity;
```

---

# Step 5. Create the Partitioned Orders Table

Run:

```sql
CREATE TABLE orders_partitioned
WITH (
    format = 'PARQUET',
    parquet_compression = 'SNAPPY',
    external_location = 's3://ai-supplychain-datalake/processed/orders-partitioned/',
    partitioned_by = ARRAY['year', 'month']
)
AS
SELECT
    order_id,
    customer_id,
    product_id,
    warehouse_id,
    quantity,
    unit_price,
    order_status,
    order_date,
    substr(order_date, 1, 4) AS year,
    substr(order_date, 6, 2) AS month
FROM orders;
```

Expected structure:

```text
processed/orders-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

---

# Step 6. Verify the Orders Table

Run:

```sql
SELECT *
FROM orders_partitioned
LIMIT 10;
```

Query orders from July 2026:

```sql
SELECT
    order_id,
    customer_id,
    order_status,
    quantity,
    unit_price,
    order_date
FROM orders_partitioned
WHERE year = '2026'
  AND month = '07'
ORDER BY order_date;
```

Calculate revenue by warehouse:

```sql
SELECT
    warehouse_id,
    SUM(quantity * unit_price) AS total_revenue
FROM orders_partitioned
WHERE year = '2026'
  AND month = '07'
  AND order_status <> 'CANCELLED'
GROUP BY warehouse_id
ORDER BY total_revenue DESC;
```

---

# Step 7. Create the Partitioned Shipments Table

Run:

```sql
CREATE TABLE shipments_partitioned
WITH (
    format = 'PARQUET',
    parquet_compression = 'SNAPPY',
    external_location = 's3://ai-supplychain-datalake/processed/shipments-partitioned/',
    partitioned_by = ARRAY['year', 'month']
)
AS
SELECT
    shipment_id,
    order_id,
    carrier,
    origin_warehouse,
    destination,
    shipment_status,
    expected_date,
    actual_date,
    substr(expected_date, 1, 4) AS year,
    substr(expected_date, 6, 2) AS month
FROM shipments;
```

Expected structure:

```text
processed/shipments-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

---

# Step 8. Verify the Shipments Table

Run:

```sql
SELECT *
FROM shipments_partitioned
LIMIT 10;
```

Find delayed shipments:

```sql
SELECT
    shipment_id,
    order_id,
    carrier,
    destination,
    expected_date,
    actual_date,
    shipment_status
FROM shipments_partitioned
WHERE year = '2026'
  AND month = '07'
  AND (
      shipment_status = 'DELAYED'
      OR (
          actual_date IS NOT NULL
          AND actual_date <> ''
          AND CAST(actual_date AS date) > CAST(expected_date AS date)
      )
  )
ORDER BY expected_date;
```

---

# Step 9. Verify the Amazon S3 Structure

Open:

```text
Amazon S3
```

Select:

```text
ai-supplychain-datalake
```

Open:

```text
processed/
```

Expected result:

```text
processed/
│
├── inventory/
├── inventory-partitioned/
│   ├── warehouse_id=WH001/
│   ├── warehouse_id=WH002/
│   └── warehouse_id=WH003/
│
├── orders/
├── orders-partitioned/
│   └── year=2026/
│
├── shipments/
├── shipments-partitioned/
│   └── year=2026/
│
└── suppliers/
```

Athena CTAS writes the files into partition directories and adds the generated partitions to the new table metadata.

---

# Step 10. List the Partitions

Run:

```sql
SHOW PARTITIONS inventory_partitioned;
```

Expected output:

```text
warehouse_id=WH001/year=2026/month=07
warehouse_id=WH002/year=2026/month=07
warehouse_id=WH003/year=2026/month=07
```

Check Orders:

```sql
SHOW PARTITIONS orders_partitioned;
```

Expected output:

```text
year=2026/month=07
```

Check Shipments:

```sql
SHOW PARTITIONS shipments_partitioned;
```

---

# Step 11. Compare Queries With and Without Partition Filters

## Without a partition filter

```sql
SELECT
    warehouse_id,
    SUM(quantity * unit_price) AS total_revenue
FROM orders_partitioned
GROUP BY warehouse_id;
```

## With a partition filter

```sql
SELECT
    warehouse_id,
    SUM(quantity * unit_price) AS total_revenue
FROM orders_partitioned
WHERE year = '2026'
  AND month = '07'
GROUP BY warehouse_id;
```

After each query, review:

```text
Query statistics
Data scanned
Execution time
```

The difference may be small with the workshop sample. With multiple months or years of data, the partition filter enables Athena to skip unrelated folders.

---

# Step 12. Add Data to a New Partition

When data for a new month is available, use `INSERT INTO`.

Example:

```sql
INSERT INTO orders_partitioned
SELECT
    order_id,
    customer_id,
    product_id,
    warehouse_id,
    quantity,
    unit_price,
    order_status,
    order_date,
    substr(order_date, 1, 4) AS year,
    substr(order_date, 6, 2) AS month
FROM orders
WHERE substr(order_date, 1, 4) = '2026'
  AND substr(order_date, 6, 2) = '08';
```

Athena writes the records to:

```text
year=2026/month=08/
```

Do not execute the same insertion repeatedly without an idempotency or duplicate-control mechanism.

---

# Step 13. Register Existing Partitions

When another service writes data directly to a Hive-style Amazon S3 path such as:

```text
year=2026/month=08/
```

update the metadata with:

```sql
MSCK REPAIR TABLE orders_partitioned;
```

Alternatively, register the partition manually:

```sql
ALTER TABLE orders_partitioned
ADD IF NOT EXISTS
PARTITION (
    year = '2026',
    month = '08'
)
LOCATION 's3://ai-supplychain-datalake/processed/orders-partitioned/year=2026/month=08/';
```

Partitions created by the original CTAS statement are registered automatically.

---

## Drop and Recreate a CTAS Table

To recreate a table, first run:

```sql
DROP TABLE IF EXISTS inventory_partitioned;
```

`DROP TABLE` removes the table metadata from AWS Glue Data Catalog. The Parquet objects can remain in Amazon S3.

Remove the existing objects:

```bash
aws s3 rm \
s3://ai-supplychain-datalake/processed/inventory-partitioned/ \
--recursive
```

Then run the CTAS statement again.

Repeat the process for:

```text
orders-partitioned
shipments-partitioned
```

---

## Selecting Partition Keys

Select fields that:

- Frequently appear in `WHERE` conditions.
- Have a reasonable number of distinct values.
- Allow queries to exclude large amounts of unrelated data.
- Match the application's analytics patterns.

Suitable examples:

```text
year
month
day
warehouse_id
region
```

Avoid partitioning directly by highly unique values such as:

```text
order_id
customer_id
shipment_id
```

Too many partitions or small files can increase metadata overhead and query-planning time.

---

## Troubleshooting

| Issue                            | Possible cause                             | Resolution                                          |
| -------------------------------- | ------------------------------------------ | --------------------------------------------------- |
| `HIVE_PATH_ALREADY_EXISTS`       | The output path already contains data      | Delete the existing objects or use a new path       |
| Partition column not last        | Partition columns are not last in `SELECT` | Move them to the end of the column list             |
| `TABLE_ALREADY_EXISTS`           | The table already exists                   | Rename it or run `DROP TABLE`                       |
| Partitions are not displayed     | Metadata has not been registered           | Run `MSCK REPAIR TABLE`                             |
| Query still scans excessive data | No partition filter is used                | Filter by `year`, `month`, or another partition key |
| Duplicate records                | `INSERT INTO` was executed repeatedly      | Validate the target partition before inserting      |
| Incorrect year or month          | The date format is inconsistent            | Normalize dates to `YYYY-MM-DD`                     |
| Excessive partitions             | A high-cardinality field was selected      | Redesign the partition strategy                     |

---

## Best Practices

- Combine partitioning with Apache Parquet.
- Include partition filters in analytics queries.
- Select partition keys from real query patterns.
- Use Hive-style partition directories.
- Do not partition by unique identifiers.
- Avoid generating excessive small files.
- Do not repeat `INSERT INTO` without duplicate checks.
- Use a dedicated Amazon S3 location for each table.
- Review `Data scanned` after running queries.
- Consider Partition Projection when the partition count becomes very large.
- Use `string` for partition keys to support efficient partition filtering.

---

## Verification

Verify that:

- `inventory_partitioned` has been created.
- `orders_partitioned` has been created.
- `shipments_partitioned` has been created.
- The output files use Apache Parquet.
- Partition directories exist in Amazon S3.
- `SHOW PARTITIONS` returns the expected values.
- Queries can filter by `year`, `month`, and `warehouse_id`.
- No processed data has been written into the Raw Zone.
- Supplier data remains available through `suppliers_parquet`.

---

## Expected Outcome

After completing this section, you have:

- Partitioned inventory data by warehouse, year, and month.
- Partitioned order data by year and month.
- Partitioned shipment data by year and month.
- Combined Apache Parquet with partitioning.
- Verified partition metadata in Amazon Athena.
- Prepared optimized datasets for analytics and AI workloads.
