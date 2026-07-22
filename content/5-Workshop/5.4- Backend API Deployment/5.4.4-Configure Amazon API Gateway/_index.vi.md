---
title: "5.4.4 Cấu hình Amazon API Gateway"
date: 2024-01-01
weight: 4
chapter: false
---


## Tổng quan

Amazon API Gateway là dịch vụ quản lý API của AWS, cho phép tạo, triển khai và bảo vệ các RESTful API hoặc HTTP API.

Trong hệ thống **AI Supply Chain Control Tower**, API Gateway đóng vai trò là cổng giao tiếp giữa ứng dụng Frontend và Lambda Backend API. Mọi yêu cầu từ người dùng sẽ đi qua API Gateway trước khi được chuyển đến Lambda để xử lý.

Ngoài việc định tuyến yêu cầu, API Gateway còn hỗ trợ xác thực bằng Amazon Cognito, giới hạn lưu lượng truy cập (Rate Limiting), ghi log và theo dõi hiệu năng.

---

# Kiến trúc

```
Frontend
     │
     ▼
Amazon API Gateway
     │
     ▼
Lambda Backend API
     │
     ▼
Amazon RDS PostgreSQL
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo HTTP API.
- Kết nối API Gateway với Lambda.
- Triển khai API.
- Lấy Endpoint của API.
- Kiểm tra API hoạt động.

---

# Tại sao sử dụng API Gateway?

API Gateway mang lại nhiều lợi ích:

- Cung cấp Endpoint công khai cho ứng dụng.
- Tự động mở rộng theo lưu lượng truy cập.
- Hỗ trợ xác thực bằng JWT.
- Ghi log thông qua Amazon CloudWatch.
- Dễ dàng tích hợp với AWS Lambda.

---

# Các bước thực hiện

## Bước 1. Mở Amazon API Gateway

Đăng nhập AWS Management Console.

Tìm kiếm:

```
API Gateway
```

Chọn:

```
HTTP APIs
```

![API Gateway Dashboard](/images/5-Workshop/5.4-S3-onprem/5.4.4.png)

---

## Bước 2. Tạo API

Nhấn:

```
Create API
```

Chọn:

```
HTTP API
```

Sau đó chọn:

```
Build
```

---

## Bước 3. Thêm Integration

Chọn:

```
AWS Lambda
```

Sau đó chọn Lambda Function:

```
ai-supplychain-backend
```

Nhấn:

```
Next
```

---

## Bước 4. Cấu hình Route

Thêm các Route:

| Method | Path        |
| ------ | ----------- |
| GET    | /health     |
| GET    | /products   |
| POST   | /products   |
| GET    | /orders     |
| POST   | /orders     |
| POST   | /ai/predict |

Các Route này sẽ được chuyển đến Lambda Backend API để xử lý.

---

## Bước 5. Đặt tên API

| Thuộc tính | Giá trị            |
| ---------- | ------------------ |
| API Name   | AI-SupplyChain-API |

Nhấn:

```
Create
```

---

## Bước 6. Triển khai API

API Gateway sẽ tự động tạo Stage mặc định:

```
$default
```

Sau khi triển khai, AWS sẽ cung cấp Endpoint dạng:

```
https://xxxxxxxx.execute-api.ap-southeast-1.amazonaws.com
```

Lưu lại Endpoint này để sử dụng trong Frontend.

---

## Bước 7. Kiểm tra API

Mở trình duyệt hoặc Postman:

```
GET

https://xxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/health
```

Nếu Lambda hoạt động bình thường, kết quả sẽ là:

```json
{
  "status": "UP"
}
```

---

# Kiểm tra kết quả

Đảm bảo:

- API đã được tạo.
- Lambda Integration thành công.
- Stage `$default` hoạt động.
- Endpoint trả về dữ liệu.

---

# Best Practices

AWS khuyến nghị:

- Sử dụng HTTP API khi không cần các tính năng nâng cao của REST API để giảm chi phí và độ trễ.
- Đặt tên Route rõ ràng, nhất quán.
- Sử dụng HTTPS cho tất cả các Endpoint.
- Theo dõi log bằng Amazon CloudWatch.
- Áp dụng JWT Authorizer để bảo vệ các API yêu cầu đăng nhập.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công Amazon API Gateway.
- Kết nối API Gateway với Lambda Backend.
- Triển khai các Endpoint REST.
- Chuẩn bị môi trường để tích hợp xác thực bằng Amazon Cognito.
