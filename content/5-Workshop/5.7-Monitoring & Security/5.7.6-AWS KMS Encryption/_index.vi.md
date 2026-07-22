---
title: "5.7.6 Mã hóa dữ liệu bằng AWS Key Management Service (AWS KMS)"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.7.6 Mã hóa dữ liệu bằng AWS Key Management Service (AWS KMS)

## Tổng quan

Mã hóa dữ liệu là một trong những biện pháp quan trọng để bảo vệ thông tin trong môi trường điện toán đám mây.

AWS Key Management Service (AWS KMS) là dịch vụ quản lý khóa mã hóa của AWS, cho phép tạo và quản lý các khóa mã hóa được sử dụng bởi nhiều dịch vụ AWS.

Trong workshop này, AWS KMS sẽ được sử dụng để mã hóa:

- Amazon S3 Data Lake
- Amazon RDS PostgreSQL
- AWS Secrets Manager
- Amazon SNS
- Amazon CloudWatch Logs (tùy chọn)

---

## Kiến trúc

```text
                AWS KMS
                   │
      Customer Managed Key (CMK)
                   │
      ┌────────────┼─────────────┐
      ▼            ▼             ▼
 Amazon S3     Amazon RDS   Secrets Manager
      │            │             │
      └────────────┼─────────────┘
                   ▼
          AI Supply Chain Control Tower
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Tạo Customer Managed Key.
- Cấu hình quyền sử dụng khóa.
- Mã hóa Amazon S3.
- Mã hóa Amazon RDS.
- Mã hóa AWS Secrets Manager.
- Kiểm tra trạng thái mã hóa.

---

# Bước 1. Mở AWS KMS

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
Key Management Service
```

Chọn:

```text
AWS Key Management Service
```

---

# Bước 2. Tạo Customer Managed Key

Chọn:

```text
Customer managed keys

Create key
```

Thiết lập:

| Thuộc tính | Giá trị             |
| ---------- | ------------------- |
| Key type   | Symmetric           |
| Key usage  | Encrypt and decrypt |
| Origin     | AWS KMS             |

Alias:

```text
alias/supplychain-key
```

Description:

```text
Encryption key for AI Supply Chain Control Tower
```

Nhấn:

```text
Create key
```

---

# Bước 3. Cấu hình Key Administrators

Chọn IAM User hoặc IAM Role quản trị có quyền:

- Quản lý khóa.
- Thay đổi Key Policy.
- Xoay khóa (Rotation).

Ví dụ:

```text
SupplyChainAdmin
```

---

# Bước 4. Cấu hình Key Users

Thêm các IAM Role:

```text
BackendLambdaRole

AIWorkerLambdaRole
```

Các Role này được phép sử dụng khóa để mã hóa và giải mã dữ liệu khi truy cập các dịch vụ AWS.

---

# Bước 5. Mã hóa Amazon S3

Mở Bucket:

```text
ai-supplychain-datalake
```

Chọn:

```text
Properties

Default encryption
```

Thiết lập:

```text
AWS KMS Key

alias/supplychain-key
```

Lưu cấu hình.

Từ thời điểm này, các đối tượng mới tải lên sẽ được mã hóa bằng khóa KMS đã chọn.

---

# Bước 6. Kiểm tra Amazon RDS

Mở:

```text
Amazon RDS
```

Chọn Database.

Kiểm tra:

```text
Storage Encryption

Enabled
```

Nếu tạo mới Database, chọn:

```text
Enable encryption

AWS KMS Key

alias/supplychain-key
```

---

# Bước 7. Kiểm tra AWS Secrets Manager

Mở Secret:

```text
SupplyChainDatabaseSecret
```

Quan sát:

```text
Encryption Key
```

Đổi sang:

```text
alias/supplychain-key
```

nếu cần sử dụng Customer Managed Key.

---

# Bước 8. Mã hóa Amazon SNS (Tùy chọn)

Mở Topic:

```text
SupplyChainAlerts
```

Chọn:

```text
Encryption

Enable server-side encryption
```

KMS Key:

```text
alias/supplychain-key
```

---

# Bước 9. Kiểm tra khóa KMS

Trong AWS KMS, mở khóa:

```text
alias/supplychain-key
```

Kiểm tra:

- Key ID
- Alias
- Rotation Status
- Key Policy
- Key Users

---

## Best Practices

- Sử dụng Customer Managed Key cho các dữ liệu quan trọng.
- Bật Automatic Key Rotation.
- Hạn chế số lượng IAM Principal có quyền quản lý khóa.
- Sử dụng một khóa riêng cho từng môi trường (Development, Testing, Production).
- Theo dõi việc sử dụng khóa bằng AWS CloudTrail.

---

## Kiểm tra kết quả

Đảm bảo:

- Customer Managed Key được tạo thành công.
- Amazon S3 sử dụng khóa KMS.
- Amazon RDS được mã hóa.
- AWS Secrets Manager sử dụng khóa KMS.
- Amazon SNS được cấu hình mã hóa (nếu áp dụng).

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo Customer Managed Key bằng AWS KMS.
- Mã hóa các tài nguyên quan trọng của hệ thống.
- Tăng cường bảo mật cho dữ liệu lưu trữ và thông tin nhạy cảm.
- Chuẩn bị nền tảng đáp ứng các yêu cầu bảo mật và tuân thủ.
