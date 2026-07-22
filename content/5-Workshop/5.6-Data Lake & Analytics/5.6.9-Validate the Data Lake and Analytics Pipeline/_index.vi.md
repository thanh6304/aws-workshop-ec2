---
title: "5.6.9 Kiểm tra và xác thực hệ thống phân tích dữ liệu"
date: 2024-01-01
weight: 9
chapter: false
---

# 5.6.9 Kiểm tra và xác thực hệ thống phân tích dữ liệu

## Tổng quan

Sau khi hoàn thành toàn bộ quy trình xây dựng Data Lake, bước cuối cùng là kiểm tra và xác thực toàn bộ hệ thống.

Mục tiêu của bước này là đảm bảo:

- Dữ liệu được lưu đúng vị trí.
- Metadata được đồng bộ.
- Amazon Athena truy vấn thành công.
- Dashboard sử dụng đúng View.
- AI Worker Lambda có thể truy cập dữ liệu.

---

## Kiến trúc

```text
CSV Files
     │
     ▼
Amazon S3
     │
     ▼
Glue Data Catalog
     │
     ▼
Athena
     │
     ▼
Views
     │
     ├── Dashboard
     └── AI Worker
```

---

## Mục tiêu

Sau bài này bạn sẽ:

- Kiểm tra toàn bộ Data Lake.
- Kiểm tra Glue Catalog.
- Kiểm tra Athena.
- Kiểm tra Dashboard Views.
- Kiểm tra AI Pipeline.

---

# Bước 1. Kiểm tra Amazon S3

Đảm bảo Bucket có cấu trúc:

```text
raw/

processed/

analytics/

athena-results/
```

Trong thư mục:

```text
processed/
```

Đảm bảo có:

```text
inventory/

orders/

shipments/

suppliers/

inventory-partitioned/

orders-partitioned/

shipments-partitioned/
```

---

# Bước 2. Kiểm tra Glue Catalog

Mở:

```text
AWS Glue
```

Kiểm tra Database:

```text
supplychain_catalog
```

Kiểm tra các bảng:

```text
inventory_partitioned

orders_partitioned

shipments_partitioned

suppliers_parquet
```

---

# Bước 3. Kiểm tra Partition

Athena

```sql
SHOW PARTITIONS inventory_partitioned;
```

```sql
SHOW PARTITIONS orders_partitioned;
```

```sql
SHOW PARTITIONS shipments_partitioned;
```

Đảm bảo các phân vùng hiển thị đầy đủ.

---

# Bước 4. Kiểm tra dữ liệu

Inventory

```sql
SELECT *
FROM inventory_partitioned
LIMIT 10;
```

Orders

```sql
SELECT *
FROM orders_partitioned
LIMIT 10;
```

Shipments

```sql
SELECT *
FROM shipments_partitioned
LIMIT 10;
```

Suppliers

```sql
SELECT *
FROM suppliers_parquet
LIMIT 10;
```

---

# Bước 5. Kiểm tra Dashboard Views

```sql
SHOW VIEWS;
```

Kiểm tra:

```text
vw_inventory_summary

vw_order_revenue

vw_order_status

vw_shipment_summary

vw_supplier_performance

vw_dashboard_kpi
```

---

# Bước 6. Kiểm tra View

```sql
SELECT *
FROM vw_dashboard_kpi;
```

```sql
SELECT *
FROM vw_inventory_summary;
```

```sql
SELECT *
FROM vw_supplier_performance;
```

---

# Bước 7. Kiểm tra Query Statistics

Sau mỗi truy vấn.

Quan sát:

```text
Execution Time

Data Scanned

Engine Version
```

Đảm bảo:

```text
Data Scanned
```

thấp hơn khi sử dụng:

```text
Parquet

Partition

Views
```

---

# Bước 8. Kiểm tra AI Worker

AI Worker Lambda chạy:

```sql
SELECT *
FROM vw_dashboard_kpi;
```

Kết quả được gửi tới:

```text
Amazon Bedrock
```

Bedrock sinh báo cáo AI.

Ví dụ:

```text
Inventory levels remain healthy.

Warehouse WH002 requires replenishment.

Supplier A has the highest quality score.

Shipment delays increased by 5%.
```

---

# Bước 9. Kiểm tra Dashboard

Dashboard hiển thị:

```text
Total Orders

Total Revenue

Inventory Value

Low Stock

Delayed Shipments

Top Suppliers
```

Đảm bảo số liệu khớp với Athena.

---

# Bước 10. Kiểm tra CloudWatch

Mở:

```text
CloudWatch
```

Kiểm tra:

```text
Athena Queries

Lambda Logs

Errors

Duration
```

Đảm bảo:

```text
No Errors
```

---

## Checklist

| Thành phần     | Trạng thái |
| -------------- | ---------- |
| Amazon S3      | ✅         |
| Glue Catalog   | ✅         |
| Athena         | ✅         |
| Partition      | ✅         |
| Views          | ✅         |
| Dashboard      | ✅         |
| Lambda Worker  | ✅         |
| Amazon Bedrock | ✅         |
| CloudWatch     | ✅         |

---

## Best Practices

- Kiểm tra Data Scanned sau mỗi truy vấn.
- Không truy vấn trực tiếp dữ liệu CSV.
- Chỉ sử dụng bảng Parquet.
- Luôn dùng Partition Filter.
- Dashboard chỉ nên sử dụng View.
- AI chỉ nên truy cập View hoặc bảng đã tối ưu.

---

## Kiểm tra kết quả

Đảm bảo:

- Tất cả truy vấn thực thi thành công.
- Dashboard hiển thị dữ liệu chính xác.
- AI Worker Lambda hoạt động bình thường.
- Không có lỗi trong CloudWatch.
- Dữ liệu được lưu đúng vị trí trong Amazon S3.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Xây dựng hoàn chỉnh Data Lake trên AWS.
- Tối ưu dữ liệu bằng Apache Parquet và Partitioning.
- Đồng bộ metadata với AWS Glue Data Catalog.
- Phân tích dữ liệu bằng Amazon Athena.
- Chuẩn hóa dữ liệu bằng Dashboard Views.
- Chuẩn bị dữ liệu cho AI Worker Lambda và Amazon Bedrock.
