---
title: "5.3.7 Tạo IAM Roles cho Lambda"
date: 2024-01-01
weight: 7
chapter: false
---

# 5.3.7 Tạo IAM Roles cho Lambda

## Tổng quan

AWS Identity and Access Management (IAM) là dịch vụ quản lý danh tính và quyền truy cập trong AWS.

Trong hệ thống **AI Supply Chain Control Tower**, các hàm AWS Lambda cần quyền truy cập đến nhiều dịch vụ AWS như Amazon RDS, Amazon S3, Amazon SQS, Amazon Athena, Amazon Bedrock và AWS Secrets Manager.

Thay vì sử dụng Access Key hoặc Secret Key trong mã nguồn, AWS khuyến nghị sử dụng **IAM Role** để cấp quyền cho Lambda theo nguyên tắc **Least Privilege** (chỉ cấp đúng quyền cần thiết).

---

# Kiến trúc

```
               IAM Role
                   │
      ┌────────────┼────────────┐
      │            │            │
      ▼            ▼            ▼
 Amazon S3     Amazon SQS   CloudWatch
      │
      ▼
 Athena
      │
      ▼
 Bedrock
      │
      ▼
Secrets Manager
      │
      ▼
 Lambda Function
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo IAM Role cho Lambda.
- Gán các AWS Managed Policies cần thiết.
- Hiểu nguyên tắc Least Privilege.
- Chuẩn bị quyền truy cập cho Backend và AI Pipeline.

---

# Các bước thực hiện

## Bước 1. Mở IAM Console

Đăng nhập AWS Management Console.

Tìm kiếm:

```
IAM
```

Chọn:

```
Roles
```

![Kiến trúc IAM](/images/5-Workshop/5.3-S3-vpc/5.3.7.png)

---

## Bước 2. Tạo Role mới

Chọn:

```
Create role
```

Trong phần **Trusted entity type**:

```
AWS Service
```

Service:

```
Lambda
```

Nhấn:

```
Next
```

> Chèn hình: Create IAM Role

---

## Bước 3. Thêm quyền

Tìm và chọn các chính sách sau:

| Policy                       | Mục đích                       |
| ---------------------------- | ------------------------------ |
| AWSLambdaBasicExecutionRole  | Ghi log lên CloudWatch         |
| AmazonS3FullAccess _(Demo)_  | Đọc/Ghi dữ liệu S3             |
| AmazonSQSFullAccess _(Demo)_ | Gửi và nhận Message            |
| AmazonAthenaFullAccess       | Truy vấn Athena                |
| AWSGlueConsoleFullAccess     | Đọc Glue Data Catalog          |
| AmazonBedrockFullAccess      | Gọi mô hình AI                 |
| SecretsManagerReadWrite      | Đọc thông tin kết nối Database |

> **Lưu ý:** Trong môi trường Production, không nên sử dụng các quyền `FullAccess`. Thay vào đó, hãy tạo **IAM Policy** chỉ cấp đúng quyền cần thiết.

---

## Bước 4. Đặt tên Role

Nhập:

| Thuộc tính | Giá trị                   |
| ---------- | ------------------------- |
| Role Name  | AI-SupplyChain-LambdaRole |

Nhấn:

```
Create Role
```

> Chèn hình: IAM Role Summary

---

## Bước 5. Kiểm tra Role

Sau khi tạo thành công, kiểm tra:

- Trusted Entity = Lambda
- Attached Policies đúng như đã cấu hình

---

# Best Practices

AWS khuyến nghị:

- Không sử dụng Access Key trong mã nguồn.
- Sử dụng IAM Role thay cho tài khoản IAM User.
- Chỉ cấp quyền tối thiểu cần thiết (Least Privilege).
- Thường xuyên rà soát và loại bỏ các quyền không còn sử dụng.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công IAM Role cho Lambda.
- Cấp quyền truy cập các dịch vụ AWS cần thiết.
- Chuẩn bị môi trường để triển khai Backend API và AI Pipeline.
