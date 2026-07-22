---
title: "5.4.2 Cấu hình AWS Secrets Manager"
date: 2024-01-01
weight: 2
chapter: false
---


## Tổng quan

AWS Secrets Manager là dịch vụ quản lý bí mật (Secrets Management) của AWS, cho phép lưu trữ và quản lý an toàn các thông tin nhạy cảm như:

- Tên đăng nhập cơ sở dữ liệu
- Mật khẩu cơ sở dữ liệu
- API Keys
- OAuth Tokens
- SSH Keys

Trong hệ thống **AI Supply Chain Control Tower**, Lambda Backend sẽ không lưu trực tiếp thông tin kết nối PostgreSQL trong mã nguồn. Thay vào đó, Lambda sẽ đọc thông tin từ AWS Secrets Manager khi cần kết nối đến Amazon RDS.

Việc này giúp giảm rủi ro rò rỉ thông tin xác thực và phù hợp với các khuyến nghị bảo mật của AWS.

---

# Kiến trúc

```
Lambda Backend
        │
        │ Get Secret
        ▼
AWS Secrets Manager
        │
        ▼
Host
Username
Password
Database
Port
        │
        ▼
Amazon RDS PostgreSQL
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo một Secret mới.
- Lưu thông tin kết nối PostgreSQL.
- Thiết lập quyền truy cập cho Lambda.
- Kiểm tra Secret đã được tạo thành công.

---

# Tại sao sử dụng AWS Secrets Manager?

Nếu lưu thông tin kết nối trong mã nguồn:

```properties
spring.datasource.username=postgres
spring.datasource.password=123456
```

Khi mã nguồn được đưa lên GitHub hoặc chia sẻ với người khác, thông tin xác thực có thể bị lộ.

AWS Secrets Manager giúp:

- Lưu trữ thông tin mã hóa an toàn.
- Kiểm soát quyền truy cập thông qua IAM.
- Hỗ trợ xoay vòng (Rotation) mật khẩu tự động.
- Dễ dàng thay đổi thông tin mà không cần sửa mã nguồn.

---

# Các bước thực hiện

## Bước 1. Mở AWS Secrets Manager

Đăng nhập AWS Management Console.

Tìm kiếm:

```
Secrets Manager
```

Chọn:

```
Secrets
```

![AI Supply Chain Architecture](/images/5-Workshop/5.4-S3-onprem/5.4.2.png)

---

## Bước 2. Tạo Secret mới

Chọn:

```
Store a new secret
```

---

## Bước 3. Chọn loại Secret

Chọn:

```
Credentials for Amazon RDS database
```

Nếu không sử dụng tùy chọn này, bạn có thể chọn:

```
Other type of secret
```

và nhập dữ liệu theo định dạng JSON.

---

## Bước 4. Nhập thông tin kết nối

Nhập các thông tin của Amazon RDS PostgreSQL:

| Thuộc tính | Giá trị                                                      |
| ---------- | ------------------------------------------------------------ |
| username   | postgres                                                     |
| password   | **\*\*\*\***                                                 |
| host       | ai-supplychain-db.xxxxxxxxx.ap-southeast-1.rds.amazonaws.com |
| port       | 5432                                                         |
| database   | supplychain                                                  |

Ví dụ JSON:

```json
{
  "username": "postgres",
  "password": "YourStrongPassword",
  "host": "ai-supplychain-db.xxxxxxxxx.ap-southeast-1.rds.amazonaws.com",
  "port": 5432,
  "database": "supplychain"
}
```

> Chèn hình: Secret JSON

---

## Bước 5. Đặt tên Secret

Nhập:

| Thuộc tính  | Giá trị                  |
| ----------- | ------------------------ |
| Secret Name | ai-supplychain-db-secret |

Có thể thêm mô tả:

```
Database credentials for AI Supply Chain Control Tower
```

---

## Bước 6. Cấu hình Rotation

Đối với Workshop:

```
Disable automatic rotation
```

Trong môi trường Production, AWS khuyến nghị bật tính năng **Automatic Rotation** để thay đổi mật khẩu định kỳ.

---

## Bước 7. Hoàn tất

Nhấn:

```
Store
```

Secret sẽ xuất hiện trong danh sách Secrets.

---

# Cấp quyền cho Lambda

Để Lambda có thể đọc Secret, IAM Role của Lambda cần được cấp quyền:

```json
{
  "Effect": "Allow",
  "Action": ["secretsmanager:GetSecretValue"],
  "Resource": "arn:aws:secretsmanager:*:*:secret:ai-supplychain-db-secret*"
}
```

Trong workshop, quyền này đã được cấp thông qua IAM Role ở chương **5.3.7**.

---

# Kiểm tra kết quả

Kiểm tra:

- Secret hiển thị trong danh sách.
- Secret có đúng tên.
- Giá trị được lưu dưới dạng JSON.
- Lambda Role có quyền truy cập.

---

# Best Practices

AWS khuyến nghị:

- Không lưu thông tin xác thực trong mã nguồn.
- Không commit file chứa mật khẩu lên Git.
- Sử dụng IAM Role thay vì Access Key.
- Bật Automatic Rotation trong Production.
- Chỉ cấp quyền `GetSecretValue` cho các dịch vụ thực sự cần.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công Database Secret.
- Lưu thông tin kết nối PostgreSQL một cách an toàn.
- Chuẩn bị môi trường để Lambda Backend kết nối đến Amazon RDS mà không cần hard-code thông tin đăng nhập.
