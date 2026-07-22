---
title: "5.4.3 Triển khai Lambda Backend API"
date: 2024-01-01
weight: 3
chapter: false
---


## Tổng quan

AWS Lambda là dịch vụ **Function as a Service (FaaS)** của AWS, cho phép thực thi mã nguồn mà không cần quản lý máy chủ.

Trong hệ thống **AI Supply Chain Control Tower**, Lambda Backend API đóng vai trò là tầng xử lý nghiệp vụ (Business Logic Layer). Sau khi nhận yêu cầu từ Amazon API Gateway, Lambda sẽ:

- Xác thực JWT Token.
- Đọc thông tin kết nối từ AWS Secrets Manager.
- Kết nối Amazon RDS PostgreSQL.
- Thực hiện các thao tác CRUD.
- Gửi yêu cầu xử lý AI vào Amazon SQS.
- Ghi log lên Amazon CloudWatch.

Nhờ đó toàn bộ backend được xây dựng theo mô hình **Serverless**, giúp giảm chi phí vận hành và tự động mở rộng theo lưu lượng truy cập.

---

# Kiến trúc

```
Client
   │
   ▼
Amazon API Gateway
   │
   ▼
Lambda Backend API
   │
   ├────────► AWS Secrets Manager
   │
   ├────────► Amazon RDS PostgreSQL
   │
   ├────────► Amazon SQS
   │
   └────────► Amazon CloudWatch
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo Lambda Function.
- Cấu hình Runtime.
- Gán IAM Role.
- Kết nối VPC.
- Cấu hình Environment Variables.
- Kiểm thử Lambda.

---

# Lambda Backend thực hiện những gì?

Lambda Backend chịu trách nhiệm:

- Đăng nhập người dùng.
- Quản lý sản phẩm.
- Quản lý kho.
- Quản lý đơn hàng.
- Gửi yêu cầu AI.
- Trả dữ liệu về API Gateway.

Lambda **không xử lý AI trực tiếp** mà chỉ gửi yêu cầu đến Amazon SQS.

---

# Các bước thực hiện

## Bước 1. Mở AWS Lambda

Đăng nhập AWS Console.

Tìm kiếm:

```
Lambda
```

Chọn:

```
Functions
```

![Lambda Dashboard](/images/5-Workshop/5.4-S3-onprem/5.4.2.png)

---

## Bước 2. Tạo Function

Nhấn:

```
Create Function
```

Chọn:

```
Author from scratch
```

---

## Bước 3. Cấu hình Function

| Thuộc tính    | Giá trị                |
| ------------- | ---------------------- |
| Function Name | ai-supplychain-backend |
| Runtime       | Java 21                |
| Architecture  | x86_64                 |

> Nếu backend của bạn sử dụng Node.js hoặc Python, hãy chọn Runtime tương ứng.

---

## Bước 4. Chọn IAM Role

Trong mục **Permissions**:

Chọn:

```
Use an existing role
```

Role:

```
AI-SupplyChain-LambdaRole
```

Role này đã được tạo ở mục **5.3.7** và có quyền truy cập:

- Amazon S3
- Amazon SQS
- Amazon Athena
- Amazon Bedrock
- AWS Secrets Manager
- Amazon CloudWatch

---

## Bước 5. Cấu hình VPC

Để Lambda truy cập được Amazon RDS PostgreSQL trong Private Subnet, cần cấu hình:

| Thuộc tính     | Giá trị                            |
| -------------- | ---------------------------------- |
| VPC            | AI-SupplyChain-VPC                 |
| Subnets        | Private-Subnet-A, Private-Subnet-B |
| Security Group | Lambda-SG                          |

> Chèn hình: Lambda VPC Configuration

---

## Bước 6. Cấu hình Environment Variables

Trong tab **Configuration** → **Environment Variables**, thêm các biến:

| Key         | Value                    |
| ----------- | ------------------------ |
| SECRET_NAME | ai-supplychain-db-secret |
| AWS_REGION  | ap-southeast-1           |
| QUEUE_NAME  | ai-processing-queue      |

Các biến này giúp Lambda dễ dàng thay đổi cấu hình mà không cần sửa mã nguồn.

---

## Bước 7. Upload mã nguồn

Trong mục **Code**, chọn:

```
Upload from
```

→

```
.zip file
```

Tải lên file build của ứng dụng Backend.

Ví dụ:

```
backend-api.zip
```

Sau khi tải lên, Lambda sẽ tự động cập nhật phiên bản mới.

---

## Bước 8. Cấu hình Handler

Đối với Java:

```
com.example.Handler::handleRequest
```

Đối với Node.js:

```
index.handler
```

Đối với Python:

```
lambda_function.lambda_handler
```

Hãy sử dụng đúng Handler theo ngôn ngữ lập trình của dự án.

---

## Bước 9. Điều chỉnh Timeout và Memory

Trong **General Configuration**, cập nhật:

| Thuộc tính | Giá trị    |
| ---------- | ---------- |
| Memory     | 1024 MB    |
| Timeout    | 30 Seconds |

Thông số này phù hợp với backend thực hiện truy vấn cơ sở dữ liệu và gửi thông điệp đến Amazon SQS.

---

## Bước 10. Triển khai

Nhấn:

```
Deploy
```

AWS sẽ triển khai phiên bản mới của Lambda.

---

## Bước 11. Kiểm thử

Chọn:

```
Test
```

Tạo một Test Event:

```json
{
  "path": "/health",
  "httpMethod": "GET"
}
```

Nếu cấu hình chính xác, Lambda sẽ trả về:

```json
{
  "statusCode": 200,
  "body": "Backend API is running"
}
```

---

# Kiểm tra kết quả

Đảm bảo:

- Lambda ở trạng thái **Active**.
- IAM Role được gán chính xác.
- VPC Configuration hiển thị đúng Subnets và Security Group.
- Environment Variables đã được cấu hình.
- Test Event trả về kết quả thành công.

---

# Best Practices

AWS khuyến nghị:

- Không lưu thông tin nhạy cảm trong Environment Variables.
- Sử dụng AWS Secrets Manager để quản lý thông tin xác thực.
- Chỉ cấu hình Lambda trong VPC khi cần truy cập tài nguyên nội bộ như Amazon RDS.
- Điều chỉnh Memory và Timeout phù hợp với khối lượng xử lý.
- Theo dõi log và hiệu năng bằng Amazon CloudWatch.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công Lambda Backend API.
- Gán IAM Role và cấu hình VPC.
- Kết nối Lambda với AWS Secrets Manager.
- Chuẩn bị Lambda để làm việc với Amazon RDS PostgreSQL và Amazon SQS.
- Hoàn thành tầng xử lý nghiệp vụ của hệ thống AI Supply Chain Control Tower.
