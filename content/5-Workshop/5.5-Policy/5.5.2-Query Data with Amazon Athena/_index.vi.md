---
title: "5.5.2 Truy vấn dữ liệu bằng Amazon Athena"
date: 2024-01-01
weight: 2
chapter: false
---

# 5.5.2 Truy vấn dữ liệu bằng Amazon Athena

## Tổng quan

Amazon Athena là dịch vụ phân tích dữ liệu không máy chủ (Serverless Interactive Query Service) cho phép thực hiện truy vấn SQL trực tiếp trên dữ liệu lưu trữ trong Amazon S3 mà không cần xây dựng hoặc quản lý máy chủ cơ sở dữ liệu.

Trong hệ thống **AI Supply Chain Control Tower**, Amazon Athena được sử dụng để truy vấn dữ liệu từ Amazon S3 Data Lake thông qua AWS Glue Data Catalog. Kết quả truy vấn sẽ được Lambda Worker gửi đến Amazon Bedrock để tạo báo cáo và khuyến nghị bằng AI.

Athena đặc biệt phù hợp với các bài toán phân tích dữ liệu vì chỉ tính phí theo dung lượng dữ liệu được quét và không yêu cầu quản trị hạ tầng.

---

# Kiến trúc

```
Amazon S3 Data Lake
        │
        ▼
AWS Glue Data Catalog
        │
        ▼
Amazon Athena
        │
        ▼
Lambda Worker
        │
        ▼
Amazon Bedrock
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Cấu hình Amazon Athena.
- Thiết lập vị trí lưu kết quả truy vấn.
- Thực hiện truy vấn SQL.
- Phân tích dữ liệu chuỗi cung ứng.
- Chuẩn bị dữ liệu cho Amazon Bedrock.

---

# Các bước thực hiện

## Bước 1. Mở Amazon Athena

Đăng nhập AWS Management Console.

Tìm kiếm:

```
Amazon Athena
```

![Amazon Athena Dashboard ](/images/5-Workshop/5.5-Policy/5.5.2.png)

---

## Bước 2. Thiết lập Query Result Location

Trong lần sử dụng đầu tiên, chọn:

```
Edit settings
```

Thiết lập:

```
Query result location

↓

s3://ai-supplychain-datalake/athena-results/
```

Nhấn:

```
Save
```

Athena sẽ lưu toàn bộ kết quả truy vấn vào thư mục này.

> Chèn hình: Athena Settings

---

## Bước 3. Chọn Data Source

Trong giao diện Athena:

```
Data Source

↓

AwsDataCatalog
```

Database:

```
supplychain_catalog
```

Các bảng sẽ hiển thị:

```
inventory

orders

shipments

reports
```

---

## Bước 4. Thực hiện truy vấn

Ví dụ 1:

Hiển thị toàn bộ sản phẩm:

```sql
SELECT *
FROM inventory
LIMIT 10;
```

---

Ví dụ 2:

Các sản phẩm có tồn kho thấp:

```sql
SELECT
product_name,
quantity
FROM inventory
WHERE quantity < 20
ORDER BY quantity ASC;
```

---

Ví dụ 3:

Đếm số đơn hàng:

```sql
SELECT COUNT(*)
FROM orders;
```

---

Ví dụ 4:

Tổng doanh thu:

```sql
SELECT
SUM(total_amount) AS revenue
FROM orders;
```

---

Ví dụ 5:

Các lô hàng giao trễ:

```sql
SELECT
shipment_id,
status,
delivery_date
FROM shipments
WHERE status='Delayed';
```

---

## Bước 5. Thực thi truy vấn

Nhấn:

```
Run
```

Athena sẽ thực hiện truy vấn và hiển thị kết quả trong vài giây.

Đồng thời kết quả sẽ được lưu trong:

```
s3://ai-supplychain-datalake/athena-results/
```

---

## Bước 6. Kiểm tra kết quả

Ví dụ:

| Product        | Quantity |
| -------------- | -------- |
| Laptop Dell    | 8        |
| Mouse Logitech | 12       |
| Keyboard       | 15       |

Những dữ liệu này sẽ được Lambda Worker sử dụng để xây dựng Prompt gửi đến Amazon Bedrock.

---

# Ví dụ luồng dữ liệu

```
Inventory.csv
        │
        ▼
Glue Catalog
        │
        ▼
Athena Query

↓

Low Stock Products
        │
        ▼
Lambda Worker

↓

Amazon Bedrock
```

---

# Kiểm tra kết quả

Đảm bảo:

- Athena kết nối thành công với Glue Data Catalog.
- Truy vấn SQL thực thi thành công.
- Kết quả được lưu trong S3.
- Các bảng hiển thị đầy đủ dữ liệu.

---

# Best Practices

AWS khuyến nghị:

- Chỉ truy vấn các cột cần thiết thay vì sử dụng `SELECT *` trong môi trường Production.
- Lưu dữ liệu theo định dạng Parquet hoặc ORC để giảm chi phí và tăng hiệu năng.
- Phân vùng (Partition) dữ liệu theo ngày hoặc khu vực nếu dữ liệu lớn.
- Theo dõi lượng dữ liệu được quét để tối ưu chi phí Athena.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Cấu hình thành công Amazon Athena.
- Truy vấn dữ liệu từ Amazon S3 Data Lake.
- Phân tích dữ liệu chuỗi cung ứng bằng SQL.
- Chuẩn bị dữ liệu đầu vào cho Amazon Bedrock.
