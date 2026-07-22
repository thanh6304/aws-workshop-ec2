---
title: "5.6.8 Create Dashboard Views"
date: 2024-01-01
weight: 8
chapter: false
---

# 5.6.8 Create Dashboard Views

## Overview

After building analytical SQL queries, the next step is to create reusable **Views** in Amazon Athena.

A View is a virtual table that stores a SQL query definition instead of physical data.

By using Views, dashboards and AI applications can query standardized datasets without rewriting complex SQL statements.

For example:

```sql
SELECT *
FROM vw_inventory_summary;
```

or

```sql
SELECT *
FROM vw_order_revenue;
```

Benefits include:

- Simplified queries
- Reusable business logic
- Easier maintenance
- Consistent data models for dashboards and AI

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
      ▼
Views
      │
      ├── Inventory Dashboard
      ├── Orders Dashboard
      ├── Shipment Dashboard
      └── Supplier Dashboard
              │
              ▼
Dashboard / AI Worker Lambda
```

---

## Objectives

After completing this section, you will:

- Create reusable Athena Views.
- Standardize analytics datasets.
- Simplify dashboard development.
- Prepare datasets for AI applications.

---

# View 1. Inventory Summary

```sql
CREATE OR REPLACE VIEW vw_inventory_summary AS
SELECT
warehouse_id,
COUNT(*) AS total_products,
SUM(quantity) AS total_quantity,
SUM(quantity * unit_cost) AS inventory_value
FROM inventory_partitioned
GROUP BY warehouse_id;
```

---

# View 2. Order Revenue

```sql
CREATE OR REPLACE VIEW vw_order_revenue AS
SELECT
warehouse_id,
SUM(quantity * unit_price) AS revenue,
COUNT(*) AS total_orders
FROM orders_partitioned
WHERE order_status <> 'CANCELLED'
GROUP BY warehouse_id;
```

---

# View 3. Order Status

```sql
CREATE OR REPLACE VIEW vw_order_status AS
SELECT
order_status,
COUNT(*) AS total_orders
FROM orders_partitioned
GROUP BY order_status;
```

---

# View 4. Shipment Summary

```sql
CREATE OR REPLACE VIEW vw_shipment_summary AS
SELECT
shipment_status,
COUNT(*) AS total_shipments
FROM shipments_partitioned
GROUP BY shipment_status;
```

---

# View 5. Delayed Shipments

```sql
CREATE OR REPLACE VIEW vw_delayed_shipments AS
SELECT
shipment_id,
order_id,
carrier,
destination,
expected_date,
actual_date
FROM shipments_partitioned
WHERE shipment_status = 'DELAYED';
```

---

# View 6. Supplier Performance

```sql
CREATE OR REPLACE VIEW vw_supplier_performance AS
SELECT
supplier_name,
country,
lead_time_days,
on_time_delivery_rate,
quality_score
FROM suppliers_parquet;
```

---

# View 7. Executive Dashboard KPI

```sql
CREATE OR REPLACE VIEW vw_dashboard_kpi AS
SELECT

(SELECT COUNT(*) FROM orders_partitioned) AS total_orders,

(SELECT COUNT(*) FROM inventory_partitioned) AS total_inventory,

(SELECT COUNT(*) FROM shipments_partitioned) AS total_shipments,

(SELECT COUNT(*) FROM suppliers_parquet) AS total_suppliers;
```

---

# View 8. Warehouse Performance

```sql
CREATE OR REPLACE VIEW vw_warehouse_performance AS
SELECT
o.warehouse_id,
COUNT(o.order_id) AS total_orders,
SUM(o.quantity * o.unit_price) AS revenue,
SUM(i.quantity) AS inventory
FROM orders_partitioned o
JOIN inventory_partitioned i
ON o.warehouse_id = i.warehouse_id
GROUP BY o.warehouse_id;
```

---

# Verify the Views

List available Views:

```sql
SHOW VIEWS;
```

Or:

```sql
SHOW TABLES;
```

Run:

```sql
SELECT *
FROM vw_dashboard_kpi;
```

---

# Dashboard Integration

Inventory Dashboard

```text
vw_inventory_summary
```

Sales Dashboard

```text
vw_order_revenue
```

Shipment Dashboard

```text
vw_shipment_summary
```

Supplier Dashboard

```text
vw_supplier_performance
```

Executive Dashboard

```text
vw_dashboard_kpi
```

---

# AI Worker Integration

The AI Worker Lambda can execute:

```sql
SELECT *
FROM vw_dashboard_kpi;
```

or

```sql
SELECT *
FROM vw_inventory_summary;
```

The query results can then be sent to **Amazon Bedrock** to generate AI-powered business insights.

---

## Best Practices

- Use the `vw_` prefix for all views.
- Keep business logic inside reusable views.
- Use views to simplify dashboards.
- Grant dashboards access to views instead of base tables when appropriate.
- Organize views by business domain (Inventory, Orders, Shipments, Suppliers).

---

## Verification

Verify that:

- All views are created successfully.
- Each view returns the expected results.
- Dashboards can query the views.
- AI Worker Lambda can access the views.

---

## Expected Outcome

After completing this section, you have:

- Created reusable Amazon Athena views.
- Standardized datasets for dashboards.
- Reduced duplicated SQL logic.
- Prepared reusable analytics datasets for AI Worker Lambda and Amazon Bedrock.
