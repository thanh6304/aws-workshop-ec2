---
title: "5.6.5 Cập nhật AWS Glue Data Catalog"
date: 2024-01-01
weight: 5
chapter: false
---


## Tổng quan

Sau khi dữ liệu đã được chuyển sang định dạng Apache Parquet và phân vùng trong Amazon S3, bước tiếp theo là cập nhật **AWS Glue Data Catalog**.

Glue Data Catalog lưu trữ metadata của các tập dữ liệu như:

- Database
- Table
- Column
- Data Type
- Partition
- Amazon S3 Location

Amazon Athena sẽ sử dụng metadata này để thực hiện các truy vấn SQL mà không cần biết cấu trúc vật lý của dữ liệu trong Amazon S3.

---

## Kiến trúc

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

## Mục tiêu

Sau bài này, bạn sẽ:

- Kiểm tra Database trong Glue Catalog.
- Kiểm tra các bảng đã tạo.
- Kiểm tra Schema.
- Kiểm tra Partition.
- Đồng bộ metadata.
- Chuẩn bị dữ liệu cho Dashboard và AI.

---

# Bước 1. Mở AWS Glue Console

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
AWS Glue
```

Chọn:

```text
Data Catalog
```

---

# Bước 2. Kiểm tra Database

Chọn:

```text
Databases
```

Đảm bảo Database:

```text
supplychain_catalog
```

đã tồn tại.

---

# Bước 3. Kiểm tra Tables

Chọn Database:

```text
supplychain_catalog
```

Kiểm tra các bảng:

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

# Bước 4. Kiểm tra Schema

Ví dụ bảng:

```text
orders_partitioned
```

Kiểm tra:

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

Đảm bảo kiểu dữ liệu phù hợp.

Ví dụ:

```text
varchar

integer

double

date
```

---

# Bước 5. Kiểm tra Partition

Mở:

```text
orders_partitioned
```

Chọn:

```text
Partitions
```

Ví dụ:

```text
year=2026/month=07
```

Inventory:

```text
warehouse_id=WH001/year=2026/month=07
```

---

# Bước 6. Cập nhật Partition

Nếu có dữ liệu mới được tải trực tiếp lên Amazon S3.

Ví dụ:

```text
year=2026/month=08/
```

Trong Athena:

```sql
MSCK REPAIR TABLE orders_partitioned;
```

Hoặc:

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

# Bước 7. Kiểm tra bằng Athena

```sql
SHOW TABLES;
```

Kiểm tra:

```sql
SHOW PARTITIONS orders_partitioned;
```

---

# Bước 8. Kiểm tra Location

Ví dụ:

```text
orders_partitioned
```

Location:

```text
s3://ai-supplychain-datalake/processed/orders-partitioned/
```

Đảm bảo Location đúng với thư mục trong Amazon S3.

---

# Best Practices

- Không sửa Schema thủ công nếu không cần.
- Sử dụng Glue Crawler khi có nhiều Dataset.
- Đồng bộ Partition thường xuyên.
- Kiểm tra Data Type trước khi phân tích.
- Đặt tên Database và Table rõ ràng.

---

## Kiểm tra kết quả

Đảm bảo:

- Database tồn tại.
- Tất cả Table xuất hiện.
- Schema chính xác.
- Partition hiển thị.
- Athena truy vấn thành công.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Cập nhật AWS Glue Data Catalog.
- Đồng bộ metadata với Amazon S3.
- Chuẩn bị dữ liệu cho Amazon Athena.
- Hoàn thiện lớp Metadata của Data Lake.
