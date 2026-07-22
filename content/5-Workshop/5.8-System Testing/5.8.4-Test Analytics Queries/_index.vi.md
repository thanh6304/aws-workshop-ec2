---
title: "5.8.4 Kiểm thử truy vấn phân tích với Amazon Athena"
date: 2024-01-01
weight: 4
chapter: false
---



## Tổng quan

Amazon Athena là dịch vụ phân tích không máy chủ (serverless) cho phép truy vấn dữ liệu trực tiếp trên Amazon S3 bằng SQL.

Trong bài này, bạn sẽ kiểm tra rằng:

- Dữ liệu đã được lưu đúng trong Data Lake.
- AWS Glue Data Catalog hoạt động chính xác.
- Amazon Athena có thể thực hiện các truy vấn phân tích.
- Kết quả truy vấn sẵn sàng cho Dashboard và AI Pipeline.

---

## Kiến trúc

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

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Kiểm tra Data Catalog.
- Thực hiện các truy vấn Athena.
- Kiểm tra Dashboard Views.
- Xác minh dữ liệu phân tích.

---

# Bước 1. Mở Amazon Athena

Đăng nhập AWS Management Console.

Mở:

```text
Amazon Athena
```

Chọn:

```text
Query Editor
```

Đảm bảo Workgroup:

```text
SupplyChainAnalytics
```

đang được sử dụng.

---

# Bước 2. Kiểm tra Data Catalog

Chọn Database:

```text
supplychain_db
```

Đảm bảo các bảng sau tồn tại:

```text
inventory_parquet

orders_parquet

shipments_parquet

suppliers_parquet
```

---

# Bước 3. Kiểm tra số lượng dữ liệu

Ví dụ:

```sql
SELECT COUNT(*)
FROM orders_parquet;
```

Kết quả mong đợi:

```text
count > 0
```

Điều này xác nhận dữ liệu đã được nạp vào Data Lake.

---

# Bước 4. Kiểm tra Dashboard View

Ví dụ:

```sql
SELECT *
FROM vw_dashboard_kpi;
```

Đảm bảo truy vấn trả về các chỉ số tổng hợp như:

- Total Orders
- Total Revenue
- Inventory Value
- Delayed Shipments

---

# Bước 5. Kiểm tra truy vấn nghiệp vụ

Ví dụ:

```sql
SELECT
product_name,
SUM(quantity) AS total_quantity
FROM orders_parquet
GROUP BY product_name
ORDER BY total_quantity DESC
LIMIT 10;
```

Kết quả hiển thị 10 sản phẩm có số lượng đặt hàng cao nhất.

---

# Bước 6. Kiểm tra dữ liệu phân vùng

Ví dụ:

```sql
SHOW PARTITIONS orders_partitioned;
```

Đảm bảo các phân vùng theo ngày hoặc tháng được nhận diện chính xác.

---

# Bước 7. Kiểm tra lịch sử truy vấn

Mở:

```text
Query History
```

Xác nhận:

- Truy vấn hoàn thành với trạng thái **SUCCEEDED**.
- Không có truy vấn ở trạng thái **FAILED**.

---

# Bước 8. Kiểm tra kết quả lưu trên Amazon S3

Mở Bucket:

```text
ai-supplychain-datalake
```

Thư mục:

```text
athena-results/
```

Đảm bảo các tệp kết quả truy vấn đã được tạo.

---

## Best Practices

- Chỉ truy vấn các cột cần thiết thay vì `SELECT *` trên các bảng lớn.
- Sử dụng bảng Parquet và Partition để giảm dữ liệu cần quét.
- Theo dõi lượng dữ liệu được quét để tối ưu chi phí.
- Sử dụng Dashboard Views cho các báo cáo thường xuyên.

---

## Kiểm tra kết quả

Đảm bảo:

- Athena kết nối thành công với AWS Glue Data Catalog.
- Truy vấn SQL thực thi thành công.
- Dashboard Views trả về dữ liệu chính xác.
- Kết quả truy vấn được lưu trong Amazon S3.
- Không có lỗi trong Query History.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Kiểm tra thành công Amazon Athena.
- Xác nhận Data Lake có thể được phân tích bằng SQL.
- Đảm bảo dữ liệu sẵn sàng cho Dashboard và AI Pipeline.
