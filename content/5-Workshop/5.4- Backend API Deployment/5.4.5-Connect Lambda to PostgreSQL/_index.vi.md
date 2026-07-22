---
title: "5.4.5 Kết nối Lambda với Amazon RDS PostgreSQL"
date: 2024-01-01
weight: 5
chapter: false
---

# 5.4.5 Kết nối Lambda với Amazon RDS PostgreSQL

## Tổng quan

Sau khi đã triển khai Amazon RDS PostgreSQL, AWS Lambda và AWS Secrets Manager, bước tiếp theo là cấu hình để Lambda có thể kết nối an toàn đến cơ sở dữ liệu.

Trong kiến trúc của AI Supply Chain Control Tower, Lambda sẽ đọc thông tin kết nối từ AWS Secrets Manager, thiết lập kết nối đến Amazon RDS PostgreSQL và thực hiện các thao tác CRUD đối với dữ liệu.

Việc tách riêng thông tin kết nối giúp tăng cường bảo mật, đồng thời dễ dàng thay đổi cấu hình mà không cần chỉnh sửa mã nguồn.

---

# Kiến trúc

```
Lambda Backend
       │
       │ GetSecretValue
       ▼
AWS Secrets Manager
       │
Database Credentials
       │
       ▼
Amazon RDS PostgreSQL
       │
       ▼
Inventory
Orders
Products
Users
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Kết nối Lambda với Amazon RDS.
- Kiểm tra kết nối PostgreSQL.
- Tạo các bảng dữ liệu.
- Thực hiện thao tác CRUD.
- Xác minh Lambda có thể truy cập Database thành công.

---

# Điều kiện tiên quyết

Trước khi thực hiện, hãy đảm bảo:

- Amazon RDS PostgreSQL đang ở trạng thái **Available**.
- Lambda đã được cấu hình VPC.
- Security Group của Lambda được phép truy cập cổng **5432**.
- Secret trong AWS Secrets Manager đã được tạo.
- IAM Role của Lambda có quyền `secretsmanager:GetSecretValue`.

---

# Các bước thực hiện

## Bước 1. Chuẩn bị cơ sở dữ liệu

Kết nối đến Amazon RDS PostgreSQL bằng pgAdmin hoặc DBeaver.

Tạo cơ sở dữ liệu (nếu chưa có):

```sql
CREATE DATABASE supplychain;
```

Sau đó tạo các bảng cần thiết.

Ví dụ bảng Product:

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200),
    quantity INTEGER,
    price NUMERIC(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Ví dụ bảng Orders:

```sql
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    customer_name VARCHAR(100),
    total_amount NUMERIC(12,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

![PostgreSQL Tables](/images/5-Workshop/5.4-S3-onprem/5.4.5.png)

---

## Bước 2. Đọc Secret trong Lambda

Lambda sẽ sử dụng AWS SDK để lấy thông tin kết nối từ Secrets Manager.

Ví dụ dữ liệu nhận được:

```json
{
  "username": "postgres",
  "password": "********",
  "host": "ai-supplychain-db.xxxxxxxxx.ap-southeast-1.rds.amazonaws.com",
  "port": 5432,
  "database": "supplychain"
}
```

Sau khi đọc Secret, Lambda tạo chuỗi kết nối PostgreSQL.

---

## Bước 3. Cấu hình Spring Boot

Trong dự án Spring Boot, cấu hình datasource sẽ được tạo động thay vì khai báo trực tiếp trong `application.yml`.

Ví dụ:

```properties
spring.datasource.url=<Generated from Secrets Manager>
spring.datasource.username=<Secret Username>
spring.datasource.password=<Secret Password>
```

Điều này giúp tránh lưu thông tin nhạy cảm trong mã nguồn.

---

## Bước 4. Kiểm tra kết nối

Triển khai phiên bản mới của Lambda.

Trong AWS Lambda Console, tạo Test Event:

```json
{
  "path": "/health"
}
```

Nếu Lambda kết nối thành công đến PostgreSQL, kết quả trả về:

```json
{
  "status": "UP",
  "database": "CONNECTED"
}
```

---

## Bước 5. Kiểm tra CRUD

Thử gọi API:

```
POST /products
```

Body:

```json
{
  "name": "Laptop Dell",
  "quantity": 15,
  "price": 1200
}
```

Sau đó gọi:

```
GET /products
```

Nếu API trả về dữ liệu vừa thêm, việc kết nối giữa Lambda và PostgreSQL đã thành công.

---

# Luồng xử lý

```
Client
   │
   ▼
API Gateway
   │
   ▼
Lambda
   │
   │ Read Secret
   ▼
Secrets Manager
   │
Database Credentials
   │
   ▼
Amazon RDS PostgreSQL
```

---

# Kiểm tra kết quả

Đảm bảo:

- Lambda thực thi thành công.
- Không xuất hiện lỗi kết nối Database.
- Dữ liệu được ghi vào PostgreSQL.
- API trả về dữ liệu chính xác.

---

# Best Practices

AWS khuyến nghị:

- Không lưu chuỗi kết nối trong mã nguồn.
- Chỉ cho phép Lambda Security Group truy cập RDS.
- Sử dụng Connection Pool nếu ứng dụng có lưu lượng lớn.
- Theo dõi lỗi kết nối thông qua Amazon CloudWatch Logs.
- Mã hóa dữ liệu nhạy cảm bằng AWS KMS.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Kết nối thành công Lambda với Amazon RDS PostgreSQL.
- Đọc thông tin kết nối từ AWS Secrets Manager.
- Thực hiện thành công các thao tác CRUD.
- Hoàn thiện tầng lưu trữ dữ liệu của Backend API.
