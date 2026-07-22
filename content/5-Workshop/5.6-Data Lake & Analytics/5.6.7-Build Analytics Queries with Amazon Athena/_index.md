---
title: "5.6.7 Build Analytics Queries with Amazon Athena"
date: 2024-01-01
weight: 7
chapter: false
---


## Overview

After the datasets have been stored in Amazon S3, registered in the AWS Glue Data Catalog, and optimized using Apache Parquet and partitioning, the next step is to analyze them with Amazon Athena.

In this section, you will build SQL queries to analyze the AI Supply Chain Control Tower datasets and generate business insights.

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
Business Analytics
      │
      ├── Inventory
      ├── Orders
      ├── Shipments
      └── Suppliers
```

---

## Objectives

After completing this section, you will:

- Query datasets using SQL.
- Analyze inventory levels.
- Measure sales revenue.
- Track order processing.
- Monitor shipment performance.
- Evaluate supplier performance.
- Prepare datasets for dashboards and AI analysis.

---

# Inventory Analytics

## Query 1. Total Products

```sql
SELECT COUNT(*) AS total_products
FROM inventory_partitioned;
```

---

## Query 2. Total Inventory

```sql
SELECT
SUM(quantity) AS total_inventory
FROM inventory_partitioned;
```

---

## Query 3. Low Stock Products

```sql
SELECT
warehouse_id,
product_name,
quantity,
reorder_level
FROM inventory_partitioned
WHERE quantity < reorder_level
ORDER BY quantity;
```

---

## Query 4. Inventory by Warehouse

```sql
SELECT
warehouse_id,
SUM(quantity) AS total_stock
FROM inventory_partitioned
GROUP BY warehouse_id
ORDER BY total_stock DESC;
```

---

# Order Analytics

## Query 5. Total Orders

```sql
SELECT COUNT(*)
FROM orders_partitioned;
```

---

## Query 6. Total Revenue

```sql
SELECT
SUM(quantity * unit_price) AS revenue
FROM orders_partitioned
WHERE order_status <> 'CANCELLED';
```

---

## Query 7. Revenue by Warehouse

```sql
SELECT
warehouse_id,
SUM(quantity * unit_price) AS revenue
FROM orders_partitioned
WHERE order_status <> 'CANCELLED'
GROUP BY warehouse_id;
```

---

## Query 8. Orders by Status

```sql
SELECT
order_status,
COUNT(*) AS total_orders
FROM orders_partitioned
GROUP BY order_status;
```

---

## Query 9. Top 5 Best-Selling Products

```sql
SELECT
product_id,
SUM(quantity) AS total_sold
FROM orders_partitioned
GROUP BY product_id
ORDER BY total_sold DESC
LIMIT 5;
```

---

# Shipment Analytics

## Query 10. Successful Deliveries

```sql
SELECT COUNT(*)
FROM shipments_partitioned
WHERE shipment_status = 'DELIVERED';
```

---

## Query 11. Delayed Shipments

```sql
SELECT
shipment_id,
order_id,
carrier,
destination
FROM shipments_partitioned
WHERE shipment_status = 'DELAYED';
```

---

## Query 12. Shipments by Carrier

```sql
SELECT
carrier,
COUNT(*) AS total_shipments
FROM shipments_partitioned
GROUP BY carrier
ORDER BY total_shipments DESC;
```

---

# Supplier Analytics

## Query 13. Top Suppliers by On-Time Delivery

```sql
SELECT
supplier_name,
on_time_delivery_rate
FROM suppliers_parquet
ORDER BY on_time_delivery_rate DESC
LIMIT 5;
```

---

## Query 14. Suppliers Under Review

```sql
SELECT
supplier_name,
quality_score,
status
FROM suppliers_parquet
WHERE status = 'REVIEW';
```

---

## Query 15. Average Lead Time

```sql
SELECT
AVG(lead_time_days) AS average_lead_time
FROM suppliers_parquet;
```

---

# Business KPIs

## Query 16. Inventory Value

```sql
SELECT
SUM(quantity * unit_cost) AS inventory_value
FROM inventory_partitioned;
```

---

## Query 17. Daily Revenue

```sql
SELECT
order_date,
SUM(quantity * unit_price) AS revenue
FROM orders_partitioned
GROUP BY order_date
ORDER BY order_date;
```

---

## Query 18. Orders by Warehouse

```sql
SELECT
warehouse_id,
COUNT(*) AS total_orders
FROM orders_partitioned
GROUP BY warehouse_id
ORDER BY total_orders DESC;
```

---

## Query 19. Delivery Success Rate

```sql
SELECT
ROUND(
100.0 *
SUM(
CASE
WHEN shipment_status = 'DELIVERED'
THEN 1
ELSE 0
END
) / COUNT(*),
2
) AS delivery_rate;
```

---

## Query 20. Dashboard Summary

```sql
SELECT
(SELECT COUNT(*) FROM orders_partitioned) AS total_orders,
(SELECT COUNT(*) FROM inventory_partitioned) AS total_inventory_items,
(SELECT COUNT(*) FROM shipments_partitioned) AS total_shipments,
(SELECT COUNT(*) FROM suppliers_parquet) AS total_suppliers;
```

---

## Best Practices

- Use partition filters whenever possible.
- Select only the required columns.
- Avoid `SELECT *` on large tables.
- Monitor the **Data Scanned** metric after each query.
- Prefer querying Parquet datasets instead of CSV datasets.

---

## Verification

Verify that:

- All SQL queries execute successfully.
- The returned results match the sample datasets.
- No SQL syntax errors occur.
- The query outputs can be used for dashboards and AI analytics.

---

## Expected Outcome

After completing this section, you have:

- Built analytical SQL queries using Amazon Athena.
- Analyzed inventory, orders, shipments, and suppliers.
- Calculated key supply chain performance indicators.
- Prepared business insights for dashboards and AI-powered decision support.
