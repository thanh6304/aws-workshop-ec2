---
title: "5.8.4 Test Analytics Queries with Amazon Athena"
date: 2024-01-01
weight: 4
chapter: false
---

# 5.8.4 Test Analytics Queries with Amazon Athena

## Overview

Amazon Athena is a serverless query service that allows you to analyze data stored in Amazon S3 using standard SQL.

In this section, you will verify that:

- Data is correctly stored in the data lake.
- AWS Glue Data Catalog is configured properly.
- Amazon Athena executes analytical queries successfully.
- Query results are available for dashboards and AI processing.

---

## Architecture

```text
Amazon S3
      │
      ▼
AWS Glue Data Catalog
      │
      ▼
Amazon Athena
      │
      ├──────────────┐
      ▼              ▼
Dashboard      AI Worker
```

---

## Objectives

After completing this section, you will:

- Verify the Data Catalog.
- Execute Athena queries.
- Test dashboard views.
- Validate analytical data.

---

# Step 1. Open Amazon Athena

Open:

```text
Amazon Athena
```

Navigate to:

```text
Query Editor
```

Verify that the active workgroup is:

```text
SupplyChainAnalytics
```

---

# Step 2. Verify the Data Catalog

Open the database:

```text
supplychain_db
```

Confirm the following tables exist:

```text
inventory_parquet

orders_parquet

shipments_parquet

suppliers_parquet
```

---

# Step 3. Verify Dataset Availability

Execute:

```sql
SELECT COUNT(*)
FROM orders_parquet;
```

Expected result:

```text
count > 0
```

---

# Step 4. Verify Dashboard Views

Execute:

```sql
SELECT *
FROM vw_dashboard_kpi;
```

Verify that the view returns KPIs such as:

- Total Orders
- Total Revenue
- Inventory Value
- Delayed Shipments

---

# Step 5. Execute a Business Query

Example:

```sql
SELECT
product_name,
SUM(quantity) AS total_quantity
FROM orders_parquet
GROUP BY product_name
ORDER BY total_quantity DESC
LIMIT 10;
```

The query should return the top 10 best-selling products.

---

# Step 6. Verify Partitions

Execute:

```sql
SHOW PARTITIONS orders_partitioned;
```

Verify that the expected partitions are available.

---

# Step 7. Review Query History

Open:

```text
Query History
```

Confirm that:

- Queries complete with a **SUCCEEDED** status.
- No unexpected **FAILED** queries appear.

---

# Step 8. Verify Query Results in Amazon S3

Open:

```text
ai-supplychain-datalake
```

Navigate to:

```text
athena-results/
```

Confirm that Athena stores query results successfully.

---

## Best Practices

- Query only required columns instead of using `SELECT *` on large datasets.
- Use Parquet and partitioned tables to minimize scanned data.
- Monitor scanned data volume to optimize costs.
- Use dashboard views for frequently accessed reports.

---

## Verification

Verify that:

- Athena connects successfully to the AWS Glue Data Catalog.
- SQL queries execute successfully.
- Dashboard views return expected data.
- Query results are stored in Amazon S3.
- Query History shows no unexpected failures.

---

## Expected Outcome

After completing this section, you have:

- Successfully validated Amazon Athena.
- Confirmed that the data lake can be queried using SQL.
- Verified that analytical data is ready for dashboards and AI processing.
