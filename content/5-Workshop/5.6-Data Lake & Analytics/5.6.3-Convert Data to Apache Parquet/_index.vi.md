---
title: "5.6.3 Chuyển dữ liệu sang định dạng Apache Parquet"
date: 2024-01-01
weight: 3
chapter: false
---

# 5.6.3 Chuyển dữ liệu sang định dạng Apache Parquet

## Tổng quan

Các tập dữ liệu vừa tải lên Amazon S3 đang ở định dạng CSV.

Mặc dù CSV dễ tạo và dễ đọc, nhưng đây không phải là định dạng tối ưu cho các truy vấn phân tích dữ liệu lớn.

Amazon Athena hoạt động hiệu quả hơn với các định dạng lưu trữ theo cột như **Apache Parquet**, vì chỉ đọc những cột cần thiết thay vì toàn bộ file.

Trong bài này, chúng ta sẽ sử dụng tính năng **CREATE TABLE AS SELECT (CTAS)** của Amazon Athena để chuyển dữ liệu từ CSV sang Apache Parquet.

---

## Kiến trúc

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

## Mục tiêu

Sau bài này bạn sẽ:

- Hiểu lợi ích của Apache Parquet.
- Chuyển dữ liệu CSV sang Parquet.
- Lưu dữ liệu vào Processed Zone.
- Kiểm tra định dạng Parquet.
- Chuẩn bị dữ liệu tối ưu cho Athena.

---

## Vì sao sử dụng Apache Parquet?

Apache Parquet là định dạng lưu trữ theo cột (Columnar Storage).

Ưu điểm:

- Giảm lượng dữ liệu Athena phải quét.
- Tăng tốc độ truy vấn.
- Hỗ trợ nén dữ liệu.
- Chỉ đọc những cột cần thiết.
- Giảm chi phí truy vấn.

Ví dụ:

CSV

```text
Athena phải đọc toàn bộ file
```

Parquet

```text
Athena chỉ đọc cột được truy vấn
```

---

# Bước 1. Mở Amazon Athena

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
Amazon Athena
```

Chọn Database:

```text
supplychain_catalog
```

Đảm bảo các bảng CSV đã được Glue Crawler tạo.

---

# Bước 2. Chuyển bảng Inventory

Thực hiện:

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

Athena sẽ:

- Đọc dữ liệu CSV.
- Chuyển sang Parquet.
- Lưu vào:

```text
processed/inventory/
```

---

# Bước 3. Chuyển bảng Orders

```sql
CREATE TABLE orders_parquet
WITH (
    format='PARQUET',
    external_location='s3://ai-supplychain-datalake/processed/orders/'
)
AS
SELECT *
FROM orders;
```

---

# Bước 4. Chuyển bảng Shipments

```sql
CREATE TABLE shipments_parquet
WITH (
    format='PARQUET',
    external_location='s3://ai-supplychain-datalake/processed/shipments/'
)
AS
SELECT *
FROM shipments;
```

---

# Bước 5. Chuyển bảng Suppliers

```sql
CREATE TABLE suppliers_parquet
WITH (
    format='PARQUET',
    external_location='s3://ai-supplychain-datalake/processed/suppliers/'
)
AS
SELECT *
FROM suppliers;
```

---

# Bước 6. Kiểm tra dữ liệu

Mở:

```text
Amazon S3
```

Đi tới:

```text
processed/
```

Ví dụ:

```text
processed/
├── inventory/
├── orders/
├── shipments/
└── suppliers/
```

Bạn sẽ thấy các file:

```text
.parquet
```

---

# Bước 7. Kiểm tra Athena

Thực hiện:

```sql
SELECT *
FROM inventory_parquet
LIMIT 10;
```

Nếu truy vấn thành công nghĩa là Athena đang đọc dữ liệu Parquet.

---

# Bước 8. So sánh hiệu năng

Ví dụ:

```sql
SELECT product_name
FROM inventory;
```

Sau đó:

```sql
SELECT product_name
FROM inventory_parquet;
```

Quan sát:

```text
Data scanned
```

Parquet sẽ quét ít dữ liệu hơn.

---

# Kiểm tra kết quả

Đảm bảo:

- Có bốn bảng Parquet.
- Có file `.parquet` trong Processed Zone.
- Athena truy vấn thành công.
- Data Scanned giảm so với CSV.

---

# Best Practices

- Không truy vấn trực tiếp CSV nếu dữ liệu lớn.
- Dùng Parquet cho dữ liệu phân tích.
- Kết hợp Parquet với Partition.
- Không lưu Parquet trong Raw Zone.
- Chỉ lưu Parquet trong Processed Zone.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Chuyển dữ liệu CSV sang Apache Parquet.
- Tối ưu dữ liệu cho Amazon Athena.
- Chuẩn bị dữ liệu cho Dashboard và AI Worker Lambda.
