---
title: "5.5.1 Tạo AWS Glue Data Catalog"
date: 2024-01-01
weight: 1
chapter: false
---


## Tổng quan

AWS Glue Data Catalog là kho lưu trữ metadata (siêu dữ liệu) tập trung của AWS. Dịch vụ này giúp quản lý thông tin về cấu trúc dữ liệu được lưu trữ trong Amazon S3 để các dịch vụ như Amazon Athena, Amazon EMR và Amazon Redshift có thể truy cập và phân tích dữ liệu.

Trong hệ thống **AI Supply Chain Control Tower**, AWS Glue Data Catalog được sử dụng để mô tả các tập dữ liệu như:

- Inventory
- Orders
- Shipments
- Reports

Các tập dữ liệu này được lưu trong Amazon S3 Data Lake và sẽ được Amazon Athena truy vấn trong các bước tiếp theo.

---

# Kiến trúc

```
Amazon S3 Data Lake
        │
        ▼
AWS Glue Crawler
        │
        ▼
AWS Glue Data Catalog
        │
        ▼
Amazon Athena
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo Glue Database.
- Tạo Glue Crawler.
- Quét dữ liệu trong Amazon S3.
- Sinh Data Catalog.
- Chuẩn bị dữ liệu cho Amazon Athena.

---

# Tại sao sử dụng Glue Data Catalog?

Amazon Athena không đọc trực tiếp cấu trúc dữ liệu từ các file CSV hoặc JSON trong Amazon S3.

Thay vào đó, Athena sử dụng AWS Glue Data Catalog để biết:

- Tên bảng.
- Kiểu dữ liệu của từng cột.
- Định dạng file.
- Vị trí lưu trữ dữ liệu trong Amazon S3.

Nhờ vậy, người dùng có thể truy vấn dữ liệu bằng SQL mà không cần nhập dữ liệu vào cơ sở dữ liệu truyền thống.

---

# Các bước thực hiện

## Bước 1. Mở AWS Glue Console

Đăng nhập AWS Management Console.

Tìm kiếm:

```
AWS Glue
```

Chọn:

```
Data Catalog
```

![AWS Glue Dashboard ](/images/5-Workshop/5.5-Policy/5.5.1.png)

---

## Bước 2. Tạo Database

Trong menu bên trái:

```
Databases

↓

Add Database
```

Nhập:

| Thuộc tính    | Giá trị             |
| ------------- | ------------------- |
| Database Name | supplychain_catalog |

Nhấn:

```
Create Database
```

> Chèn hình: Create Glue Database

---

## Bước 3. Tạo Crawler

Trong menu:

```
Crawlers

↓

Create Crawler
```

---

## Bước 4. Chọn nguồn dữ liệu

Chọn:

```
Data Source

↓

Amazon S3
```

Đường dẫn:

```
s3://ai-supplychain-datalake/
```

Crawler sẽ quét toàn bộ các thư mục:

- inventory/
- orders/
- shipments/
- reports/

---

## Bước 5. Cấu hình IAM Role

Chọn IAM Role đã tạo trước đó hoặc tạo mới.

Ví dụ:

```
AWSGlueServiceRole
```

Role này cần có quyền:

- Đọc dữ liệu từ Amazon S3.
- Ghi metadata vào Glue Data Catalog.

---

## Bước 6. Chọn Database

Chọn:

```
supplychain_catalog
```

Đặt tên Crawler:

```
SupplyChainCrawler
```

---

## Bước 7. Chạy Crawler

Nhấn:

```
Run
```

Crawler sẽ:

- Quét dữ liệu.
- Phân tích cấu trúc file.
- Sinh Metadata.
- Tạo các bảng trong Glue Data Catalog.

---

## Bước 8. Kiểm tra kết quả

Sau khi hoàn thành, mở:

```
Tables
```

Bạn sẽ thấy các bảng như:

```
inventory

orders

shipments

reports
```

Các bảng này sẽ được Amazon Athena sử dụng để thực hiện truy vấn SQL.

> Chèn hình: Glue Tables

---

# Kiểm tra kết quả

Đảm bảo:

- Glue Database đã được tạo.
- Crawler chạy thành công.
- Các bảng xuất hiện trong Data Catalog.
- Trạng thái Crawler là **Succeeded**.

---

# Best Practices

AWS khuyến nghị:

- Tổ chức dữ liệu trong Amazon S3 theo cấu trúc thư mục rõ ràng.
- Sử dụng định dạng cột như Parquet hoặc ORC trong môi trường Production để tối ưu hiệu năng truy vấn.
- Chạy lại Crawler khi cấu trúc dữ liệu thay đổi.
- Chỉ cấp quyền cần thiết cho IAM Role của Glue.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công AWS Glue Database.
- Tạo Glue Crawler.
- Sinh Data Catalog từ dữ liệu trong Amazon S3.
- Chuẩn bị môi trường để Amazon Athena thực hiện truy vấn dữ liệu.
