---
title: "5.4.7 Kiểm thử Backend APIs"
date: 2024-01-01
weight: 7
chapter: false
---


## Tổng quan

Sau khi hoàn thành việc triển khai Backend API, bước cuối cùng là kiểm tra toàn bộ luồng xử lý của hệ thống.

Trong bài này, chúng ta sẽ sử dụng **Postman** để gửi các yêu cầu HTTP đến Amazon API Gateway và xác minh rằng:

- Người dùng có thể đăng nhập bằng Amazon Cognito.
- API Gateway xác thực JWT thành công.
- Lambda Backend xử lý yêu cầu.
- Lambda đọc thông tin kết nối từ AWS Secrets Manager.
- Lambda truy cập Amazon RDS PostgreSQL.
- Lambda gửi thông điệp đến Amazon SQS.
- Nhật ký thực thi được ghi vào Amazon CloudWatch.

Đây là bước xác nhận rằng toàn bộ Backend Serverless đã được triển khai thành công.

---

# Kiến trúc kiểm thử

```
Postman
    │
    ▼
Amazon Cognito
    │
JWT Token
    │
    ▼
Amazon API Gateway
    │
    ▼
Lambda Backend
    │
 ┌──┴──────────────┐
 ▼                 ▼
Secrets Manager    Amazon SQS
 │
 ▼
Amazon RDS PostgreSQL
 │
 ▼
CloudWatch Logs
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Kiểm tra API Health Check.
- Kiểm tra xác thực JWT.
- Kiểm tra CRUD Database.
- Kiểm tra gửi Message vào Amazon SQS.
- Kiểm tra nhật ký trong CloudWatch.

---

# Chuẩn bị

Đảm bảo các dịch vụ sau đang hoạt động:

- Amazon Cognito
- Amazon API Gateway
- AWS Lambda
- AWS Secrets Manager
- Amazon RDS PostgreSQL
- Amazon SQS
- Amazon CloudWatch

---

# Bài kiểm thử 1 - Health Check

Thực hiện:

```
GET

/health
```

Kết quả mong đợi:

```json
{
  "status": "UP"
}
```

Nếu API trả về HTTP 200, Backend đã hoạt động.

---

# Bài kiểm thử 2 - Xác thực JWT

Đăng nhập bằng tài khoản đã tạo trong Cognito.

Nhận:

```text
Access Token
```

Trong Postman:

```
Authorization

Bearer Token
```

Dán Access Token.

Thực hiện:

```
GET

/products
```

Kết quả:

```
HTTP 200 OK
```

Nếu Token sai:

```
HTTP 401 Unauthorized
```

---

# Bài kiểm thử 3 - Thêm sản phẩm

Gửi yêu cầu:

```
POST

/products
```

Body:

```json
{
  "name": "Laptop Dell",
  "quantity": 20,
  "price": 1200
}
```

Kết quả:

```json
{
  "message": "Product created successfully"
}
```

---

# Bài kiểm thử 4 - Lấy danh sách sản phẩm

Gửi:

```
GET

/products
```

Ví dụ kết quả:

```json
[
  {
    "id": 1,
    "name": "Laptop Dell",
    "quantity": 20,
    "price": 1200
  }
]
```

Điều này xác nhận Lambda đã truy cập thành công Amazon RDS PostgreSQL.

---

# Bài kiểm thử 5 - Gửi yêu cầu AI

Gửi:

```
POST

/ai/predict
```

Body:

```json
{
  "warehouse": "WH001",
  "forecastDays": 30
}
```

Backend sẽ không xử lý AI trực tiếp mà gửi một thông điệp đến Amazon SQS.

Kết quả:

```json
{
  "message": "Prediction request submitted successfully"
}
```

---

# Bài kiểm thử 6 - Kiểm tra Amazon SQS

Mở:

```
Amazon SQS
```

Kiểm tra Queue:

```
ai-processing-queue
```

Đảm bảo số lượng thông điệp tăng sau khi gửi yêu cầu AI.

---

# Bài kiểm thử 7 - Kiểm tra CloudWatch

Mở:

```
Amazon CloudWatch

↓

Log Groups

↓

/aws/lambda/ai-supplychain-backend
```

Đảm bảo có Log mới.

Ví dụ:

```
Lambda Started

Database Connected

Message Sent to Amazon SQS

Execution Completed
```

---

# Bảng tổng hợp kết quả

| Chức năng           | Kết quả mong đợi |
| ------------------- | ---------------- |
| Health Check        | HTTP 200         |
| JWT Authentication  | Thành công       |
| Create Product      | Thành công       |
| Get Products        | Có dữ liệu       |
| Database Connection | Connected        |
| Send SQS Message    | Thành công       |
| CloudWatch Logs     | Có Log           |

---

# Xử lý một số lỗi thường gặp

| Lỗi                            | Nguyên nhân                                      | Cách khắc phục                        |
| ------------------------------ | ------------------------------------------------ | ------------------------------------- |
| HTTP 401 Unauthorized          | JWT không hợp lệ hoặc hết hạn                    | Đăng nhập lại để lấy Access Token mới |
| HTTP 500 Internal Server Error | Lambda hoặc Database gặp lỗi                     | Kiểm tra CloudWatch Logs              |
| Database Connection Failed     | Security Group hoặc Secrets Manager cấu hình sai | Kiểm tra VPC, SG và Secret            |
| Access Denied                  | IAM Role thiếu quyền                             | Kiểm tra IAM Policies                 |
| Queue Not Found                | Sai tên Queue                                    | Kiểm tra biến môi trường `QUEUE_NAME` |

---

# Best Practices

AWS khuyến nghị:

- Kiểm thử từng API độc lập trước khi tích hợp Frontend.
- Theo dõi lỗi thông qua Amazon CloudWatch.
- Không sử dụng Access Token đã hết hạn.
- Kiểm tra IAM Role nếu gặp lỗi truy cập dịch vụ AWS.
- Thực hiện kiểm thử định kỳ sau mỗi lần triển khai.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Kiểm thử toàn bộ Backend API.
- Xác minh xác thực bằng Amazon Cognito.
- Kiểm tra kết nối Lambda với Amazon RDS PostgreSQL.
- Kiểm tra gửi thông điệp đến Amazon SQS.
- Xác minh CloudWatch ghi nhận đầy đủ nhật ký thực thi.

Backend của hệ thống **AI Supply Chain Control Tower** đã sẵn sàng để triển khai AI Pipeline trong chương tiếp theo.
