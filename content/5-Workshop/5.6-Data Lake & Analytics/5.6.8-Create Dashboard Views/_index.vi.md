---
title: "5.6.8 Tạo Dashboard Views"
date: 2024-01-01
weight: 8
chapter: false
---


## Tổng quan

Sau khi xây dựng các truy vấn phân tích, bước tiếp theo là tạo các **View** trong Amazon Athena.

View là các truy vấn SQL được lưu lại dưới dạng bảng ảo (Virtual Table).

Thay vì phải viết lại các câu truy vấn dài, Dashboard hoặc AI Worker Lambda chỉ cần truy vấn trực tiếp các View.

Ví dụ:

```sql
SELECT *
FROM vw_inventory_summary;
```

hoặc

```sql
SELECT *
FROM vw_order_revenue;
```

Điều này giúp:

- Đơn giản hóa truy vấn.
- Tăng khả năng tái sử dụng.
- Dễ bảo trì.
- Chuẩn hóa dữ liệu cho Dashboard.

---

## Kiến trúc

```text
Amazon S3
      │
      ▼
Glue Data Catalog
      │
      ▼
Amazon Athena
      │
      ▼
Views
      │
      ├── Inventory Dashboard
      ├── Order Dashboard
      ├── Shipment Dashboard
      └── Supplier Dashboard
              │
              ▼
      AI Worker / Dashboard
```

---

## Mục tiêu

Sau bài này bạn sẽ:

- Tạo các View.
- Chuẩn hóa dữ liệu Dashboard.
- Chuẩn bị dữ liệu cho AI.
- Giảm việc lặp lại câu lệnh SQL.

---

# View 1. Inventory Summary

```sql
CREATE OR REPLACE VIEW vw_inventory_summary AS
SELECT
warehouse_id,
COUNT(*) total_products,
SUM(quantity) total_quantity,
SUM(quantity*unit_cost) inventory_value
FROM inventory_partitioned
GROUP BY warehouse_id;
```

Kiểm tra:

```sql
SELECT *
FROM vw_inventory_summary;
```

---

# View 2. Order Revenue

```sql
CREATE OR REPLACE VIEW vw_order_revenue AS
SELECT
warehouse_id,
SUM(quantity*unit_price) revenue,
COUNT(*) total_orders
FROM orders_partitioned
WHERE order_status<>'CANCELLED'
GROUP BY warehouse_id;
```

Kiểm tra:

```sql
SELECT *
FROM vw_order_revenue;
```

---

# View 3. Order Status

```sql
CREATE OR REPLACE VIEW vw_order_status AS
SELECT
order_status,
COUNT(*) total_orders
FROM orders_partitioned
GROUP BY order_status;
```

---

# View 4. Shipment Summary

```sql
CREATE OR REPLACE VIEW vw_shipment_summary AS
SELECT
shipment_status,
COUNT(*) total_shipments
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
WHERE shipment_status='DELAYED';
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

# View 7. Dashboard KPI

```sql
CREATE OR REPLACE VIEW vw_dashboard_kpi AS
SELECT

(SELECT COUNT(*)
FROM orders_partitioned)
AS total_orders,

(SELECT COUNT(*)
FROM inventory_partitioned)
AS total_inventory,

(SELECT COUNT(*)
FROM shipments_partitioned)
AS total_shipments,

(SELECT COUNT(*)
FROM suppliers_parquet)
AS total_suppliers;
```

---

# View 8. Warehouse Performance

```sql
CREATE OR REPLACE VIEW vw_warehouse_performance AS
SELECT

o.warehouse_id,

COUNT(o.order_id) total_orders,

SUM(o.quantity*o.unit_price) revenue,

SUM(i.quantity) inventory

FROM orders_partitioned o

JOIN inventory_partitioned i

ON o.warehouse_id=i.warehouse_id

GROUP BY o.warehouse_id;
```

---

# Kiểm tra View

Liệt kê toàn bộ View:

```sql
SHOW VIEWS;
```

Hoặc:

```sql
SHOW TABLES;
```

Kiểm tra:

```sql
SELECT *
FROM vw_dashboard_kpi;
```

---

# Dashboard có thể sử dụng

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

# AI Worker Lambda

Lambda có thể gọi:

```sql
SELECT *
FROM vw_dashboard_kpi;
```

hoặc

```sql
SELECT *
FROM vw_inventory_summary;
```

sau đó gửi kết quả tới:

```text
Amazon Bedrock
```

để sinh báo cáo AI.

---

## Best Practices

- Đặt tên View theo tiền tố `vw_`.
- Không lưu dữ liệu trong View.
- Sử dụng View để chuẩn hóa truy vấn.
- Chỉ cấp quyền truy cập View cho Dashboard nếu không cần truy cập trực tiếp vào bảng gốc.
- Tách View theo từng nghiệp vụ (Inventory, Orders, Shipments, Suppliers).

---

## Kiểm tra kết quả

Đảm bảo:

- Tất cả View được tạo thành công.
- Có thể truy vấn từng View.
- Dashboard sử dụng được View.
- AI Worker Lambda truy cập được View.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo các View trong Amazon Athena.
- Chuẩn hóa lớp dữ liệu cho Dashboard.
- Giảm sự trùng lặp của các truy vấn SQL.
- Chuẩn bị dữ liệu cho AI Worker Lambda và Amazon Bedrock.
