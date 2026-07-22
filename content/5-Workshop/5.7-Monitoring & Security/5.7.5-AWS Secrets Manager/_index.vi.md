---
title: "5.7.5 Bảo vệ thông tin bí mật với AWS Secrets Manager"
date: 2024-01-01
weight: 5
chapter: false
---



## Tổng quan

Các ứng dụng thường cần sử dụng nhiều thông tin nhạy cảm như:

- Tên đăng nhập cơ sở dữ liệu.
- Mật khẩu cơ sở dữ liệu.
- API Keys.
- Access Tokens.
- Chuỗi kết nối (Connection Strings).

Việc lưu các thông tin này trực tiếp trong mã nguồn hoặc file cấu hình làm tăng nguy cơ lộ dữ liệu.

AWS Secrets Manager giúp lưu trữ, quản lý và truy xuất các thông tin bí mật một cách an toàn.

Trong workshop này, Backend API và AI Worker sẽ lấy thông tin kết nối Amazon RDS PostgreSQL từ AWS Secrets Manager.

---

## Kiến trúc

```text
Backend API
        │
        ▼
AWS Secrets Manager
        │
        ▼
Database Credentials
        │
        ▼
Amazon RDS PostgreSQL
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Tạo Secret.
- Lưu thông tin kết nối cơ sở dữ liệu.
- Cấp quyền truy cập Secret cho Lambda.
- Kiểm tra việc truy xuất Secret.

---

# Bước 1. Mở AWS Secrets Manager

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
Secrets Manager
```

Chọn:

```text
AWS Secrets Manager
```

---

# Bước 2. Tạo Secret

Chọn:

```text
Store a new secret
```

Loại Secret:

```text
Credentials for Amazon RDS database
```

Ví dụ:

```text
Username

postgres
```

```text
Password

********
```

---

# Bước 3. Cấu hình thông tin Database

Nhập:

```text
Engine

PostgreSQL
```

Chọn RDS Instance đã tạo trong chương trước.

Đặt tên Secret:

```text
SupplyChainDatabaseSecret
```

Mô tả:

```text
Database credentials for AI Supply Chain Control Tower
```

Nhấn:

```text
Store
```

---

# Bước 4. Kiểm tra Secret

Mở Secret vừa tạo.

Kiểm tra:

```text
Secret name

SupplyChainDatabaseSecret
```

Quan sát:

```text
Secret ARN

Rotation

Encryption Key
```

Mặc định, Secret được mã hóa bằng AWS KMS.

---

# Bước 5. Cấp quyền cho Lambda

Mở:

```text
IAM
```

Chỉnh sửa Policy của:

```text
BackendLambdaRole
```

Thêm quyền:

```json
{
  "Effect": "Allow",
  "Action": ["secretsmanager:GetSecretValue"],
  "Resource": "arn:aws:secretsmanager:*:*:secret:SupplyChainDatabaseSecret-*"
}
```

Lặp lại cho:

```text
AIWorkerLambdaRole
```

---

# Bước 6. Truy xuất Secret trong Lambda

Lambda sẽ gọi AWS SDK để lấy Secret.

Ví dụ:

```text
Secret Name

SupplyChainDatabaseSecret
```

Sau đó:

```text
Secrets Manager

↓

Database Credentials

↓

Connect Amazon RDS
```

Không cần lưu username hoặc password trong mã nguồn.

---

# Bước 7. Kiểm tra CloudWatch Logs

Thực hiện một yêu cầu tới Backend API.

Mở:

```text
CloudWatch

Log Groups

/aws/lambda/backend-api
```

Đảm bảo:

- Lambda lấy được Secret.
- Kết nối thành công tới Amazon RDS.
- Không xuất hiện lỗi `AccessDeniedException`.

---

# Bước 8. Kiểm tra Metadata của Secret

Quan sát:

```text
Secret Name

KMS Key

Last Changed Date

Last Accessed Date
```

Thông tin này giúp theo dõi việc sử dụng Secret trong môi trường thực tế.

---

## Best Practices

- Không lưu mật khẩu trong mã nguồn.
- Sử dụng AWS Secrets Manager để quản lý thông tin nhạy cảm.
- Chỉ cấp quyền `GetSecretValue` cho các IAM Role cần thiết.
- Kích hoạt Automatic Rotation nếu ứng dụng hỗ trợ.
- Theo dõi quyền truy cập bằng AWS CloudTrail.

---

## Kiểm tra kết quả

Đảm bảo:

- Secret được tạo thành công.
- Lambda có quyền đọc Secret.
- Backend API kết nối được tới Amazon RDS.
- Không có lỗi truy cập trong CloudWatch Logs.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Lưu trữ thông tin kết nối cơ sở dữ liệu bằng AWS Secrets Manager.
- Cấp quyền truy cập Secret cho Lambda.
- Loại bỏ thông tin nhạy cảm khỏi mã nguồn.
- Nâng cao mức độ bảo mật cho AI Supply Chain Control Tower.
