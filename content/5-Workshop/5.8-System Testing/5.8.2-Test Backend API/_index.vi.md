---
title: "5.8.2 Kiểm thử Backend API"
date: 2024-01-01
weight: 2
chapter: false
---



## Tổng quan

Sau khi người dùng đăng nhập thành công và nhận được JWT Access Token từ Amazon Cognito, bước tiếp theo là kiểm tra Backend API.

Trong bài này, bạn sẽ xác nhận rằng:

- API Gateway nhận yêu cầu từ người dùng.
- JWT được xác thực thành công.
- Lambda Backend xử lý yêu cầu.
- Amazon RDS lưu và truy xuất dữ liệu chính xác.
- Backend trả về phản hồi đúng định dạng.

---

## Kiến trúc

```text
User
   │
JWT
   │
   ▼
API Gateway
   │
   ▼
Backend Lambda
   │
   ▼
Amazon RDS PostgreSQL
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Gửi yêu cầu đến Backend API.
- Kiểm tra phản hồi của API.
- Xác minh dữ liệu trong Amazon RDS.
- Theo dõi CloudWatch Logs.

---

# Bước 1. Chuẩn bị Access Token

Đăng nhập thành công thông qua Amazon Cognito.

Sao chép:

```text
Access Token
```

Token này sẽ được sử dụng trong Header của các yêu cầu API.

---

# Bước 2. Kiểm tra API Health

Gửi yêu cầu:

```http
GET /health
```

Ví dụ:

```http
GET https://your-api.execute-api.region.amazonaws.com/health
```

Kết quả mong đợi:

```json
{
  "status": "UP"
}
```

Điều này xác nhận Backend API đang hoạt động.

---

# Bước 3. Gửi yêu cầu có xác thực

Ví dụ:

```http
GET /api/inventory
```

Header:

```http
Authorization:
Bearer <Access Token>
```

Nếu xác thực thành công:

```http
200 OK
```

Ví dụ phản hồi:

```json
[
  {
    "productId": 101,
    "productName": "Laptop",
    "quantity": 250
  }
]
```

---

# Bước 4. Kiểm tra thao tác ghi dữ liệu

Gửi yêu cầu:

```http
POST /api/orders
```

Ví dụ:

```json
{
  "customerName": "John Smith",
  "productId": 101,
  "quantity": 5
}
```

Kết quả mong đợi:

```http
201 Created
```

Backend sẽ:

```text
Receive Request

↓

Validate Data

↓

Store in Amazon RDS

↓

Return Success Response
```

---

# Bước 5. Kiểm tra dữ liệu trong Amazon RDS

Mở Amazon RDS hoặc sử dụng công cụ quản lý cơ sở dữ liệu.

Thực hiện truy vấn:

```sql
SELECT *
FROM orders
ORDER BY created_at DESC;
```

Đảm bảo bản ghi vừa tạo đã được lưu thành công.

---

# Bước 6. Kiểm tra CloudWatch Logs

Mở:

```text
CloudWatch

Log Groups

/aws/lambda/backend-api
```

Kiểm tra:

- Request được ghi nhận.
- Không có lỗi Runtime.
- Không có lỗi Database Connection.

---

# Bước 7. Kiểm tra xử lý lỗi

Thử gửi yêu cầu với dữ liệu không hợp lệ.

Ví dụ:

```json
{
  "quantity": -10
}
```

Kết quả mong đợi:

```http
400 Bad Request
```

Ví dụ phản hồi:

```json
{
  "message": "Validation failed"
}
```

Điều này xác nhận Backend xử lý lỗi đầu vào đúng cách.

---

# Bước 8. Kiểm tra phản hồi không có Token

Gửi yêu cầu mà không kèm Header:

```http
Authorization
```

Kết quả mong đợi:

```http
401 Unauthorized
```

---

## Best Practices

- Luôn kiểm tra dữ liệu đầu vào trước khi lưu vào cơ sở dữ liệu.
- Trả về mã trạng thái HTTP phù hợp (`200`, `201`, `400`, `401`, `404`, `500`).
- Không trả về thông tin nhạy cảm trong phản hồi API.
- Ghi log các lỗi quan trọng để hỗ trợ điều tra và khắc phục sự cố.

---

## Kiểm tra kết quả

Đảm bảo:

- API Gateway chuyển tiếp yêu cầu đến Lambda.
- Lambda xử lý yêu cầu thành công.
- Amazon RDS lưu dữ liệu chính xác.
- CloudWatch Logs ghi nhận các yêu cầu.
- Backend trả về đúng mã trạng thái HTTP.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Xác nhận Backend API hoạt động đúng.
- Kiểm tra quá trình đọc và ghi dữ liệu với Amazon RDS.
- Đảm bảo API Gateway và Lambda Backend hoạt động ổn định.
