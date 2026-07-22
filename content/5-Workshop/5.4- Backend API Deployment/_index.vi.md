---
title: "Triển khai Backend API"
date: 2024-01-01
weight: 4
chapter: false
pre: "<b>5.4. </b>"
---

## Tổng quan

Sau khi hoàn thành việc xây dựng hạ tầng AWS, bước tiếp theo là triển khai tầng Backend cho hệ thống AI Supply Chain Control Tower.

Backend được xây dựng hoàn toàn theo kiến trúc **Serverless**, sử dụng các dịch vụ được quản lý bởi AWS nhằm giảm chi phí vận hành và tăng khả năng mở rộng.

Trong chương này, chúng ta sẽ triển khai:

- Amazon Cognito để xác thực người dùng.
- AWS Lambda để xử lý nghiệp vụ.
- Amazon API Gateway để cung cấp RESTful APIs.
- AWS Secrets Manager để lưu trữ thông tin kết nối cơ sở dữ liệu.
- Amazon RDS PostgreSQL làm cơ sở dữ liệu chính.

Sau khi hoàn thành, người dùng có thể đăng nhập, gọi API và lưu dữ liệu vào PostgreSQL một cách an toàn.

---

## Mục tiêu

Sau chương này bạn sẽ có thể:

- Tạo Amazon Cognito User Pool.
- Quản lý thông tin kết nối bằng Secrets Manager.
- Triển khai Lambda Backend.
- Cấu hình API Gateway.
- Kết nối Lambda với PostgreSQL.
- Bảo vệ API bằng JWT Token.
- Kiểm thử API bằng Postman.

---

## Kiến trúc

```
User
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
Lambda Backend API
   │
   ├────────► Secrets Manager
   │
   └────────► Amazon RDS PostgreSQL
```

---

## Nội dung

Trong chương này chúng ta sẽ thực hiện:

- 5.4.1 Create Amazon Cognito User Pool
- 5.4.2 Configure AWS Secrets Manager
- 5.4.3 Create Lambda Backend API
- 5.4.4 Configure Amazon API Gateway
- 5.4.5 Connect Lambda to PostgreSQL
- 5.4.6 Secure APIs with Cognito JWT
- 5.4.7 Test Backend APIs

---

## Kết quả

Sau khi hoàn thành chương này, Backend API sẽ hoạt động trên AWS theo kiến trúc Serverless, hỗ trợ xác thực người dùng, xử lý nghiệp vụ và lưu trữ dữ liệu một cách an toàn.
