---
title: "5.3.4 Triển khai Amazon RDS PostgreSQL"
date: 2024-01-01
weight: 4
chapter: false
---


## Tổng quan

Amazon Relational Database Service (Amazon RDS) là dịch vụ cơ sở dữ liệu được quản lý hoàn toàn (Managed Database Service) của AWS.

Thay vì tự cài đặt PostgreSQL trên EC2, Amazon RDS giúp đơn giản hóa việc triển khai, vận hành và bảo trì cơ sở dữ liệu thông qua các tính năng như:

- Tự động sao lưu (Automated Backup)
- Khôi phục dữ liệu (Point-in-Time Recovery)
- Tự động cập nhật phiên bản
- Giám sát với Amazon CloudWatch
- Mở rộng tài nguyên dễ dàng

Trong hệ thống **AI Supply Chain Control Tower**, Amazon RDS PostgreSQL được sử dụng để lưu trữ:

- Thông tin người dùng
- Danh mục sản phẩm
- Dữ liệu kho hàng
- Đơn hàng
- Kết quả phân tích AI
- Nhật ký hệ thống

---

# Kiến trúc

```
Lambda API
      │
      │ PostgreSQL 5432
      ▼
Amazon RDS PostgreSQL
      │
Private Subnet A
Private Subnet B
```

Amazon RDS được triển khai trong **Private Subnets**, không cho phép truy cập trực tiếp từ Internet nhằm tăng cường bảo mật.

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo DB Subnet Group
- Triển khai Amazon RDS PostgreSQL
- Gắn Security Group
- Thiết lập thông tin đăng nhập
- Kiểm tra trạng thái Database
- Lấy Endpoint để sử dụng trong Lambda

---

# Các bước thực hiện

## Bước 1. Mở Amazon RDS Console

Đăng nhập AWS Console.

Tìm kiếm:

```
RDS
```

Chọn:

```
Databases
```

> Chèn hình: Amazon RDS Dashboard

---

## Bước 2. Tạo DB Subnet Group

Từ menu bên trái chọn:

```
Subnet groups
```

Nhấn:

```
Create DB subnet group
```

Cấu hình:

| Thuộc tính | Giá trị |
|------------|----------|
| Name | ai-supplychain-subnet-group |
| Description | DB Subnet Group |
| VPC | AI-SupplyChain-VPC |

Chọn hai Private Subnets:

- Private-Subnet-A
- Private-Subnet-B

> Chèn hình: DB Subnet Group

---

## Bước 3. Tạo Database

Quay lại:

```
Databases
```

Chọn:

```
Create database
```

---

## Bước 4. Chọn Engine

Thiết lập:

| Thuộc tính | Giá trị |
|------------|----------|
| Creation method | Standard Create |
| Engine | PostgreSQL |
| Version | PostgreSQL 16.x (hoặc phiên bản ổn định mới nhất) |

> Chèn hình: Database Engine

---

## Bước 5. Chọn Template

Chọn:

```
Free tier
```

hoặc

```
Dev/Test
```

nếu không sử dụng Free Tier.

---

## Bước 6. Đặt tên Database

| Thuộc tính | Giá trị |
|------------|----------|
| DB Instance Identifier | ai-supplychain-db |
| Master Username | postgres |
| Master Password | ******** |

> Chèn hình: Database Settings

---

## Bước 7. Cấu hình Instance

| Thuộc tính | Giá trị |
|------------|----------|
| Instance Class | db.t3.micro |
| Storage Type | gp3 |
| Allocated Storage | 20 GB |

---

## Bước 8. Cấu hình Network

| Thuộc tính | Giá trị |
|------------|----------|
| VPC | AI-SupplyChain-VPC |
| DB Subnet Group | ai-supplychain-subnet-group |
| Public Access | No |
| Security Group | RDS-SG |

Việc tắt **Public Access** giúp cơ sở dữ liệu chỉ có thể được truy cập từ các tài nguyên nội bộ trong VPC.

---

## Bước 9. Cấu hình Additional Settings

Đặt:

| Thuộc tính | Giá trị |
|------------|----------|
| Initial Database Name | supplychain |

Có thể giữ mặc định các thông số còn lại.

---

## Bước 10. Tạo Database

Nhấn:

```
Create Database
```

AWS sẽ mất vài phút để khởi tạo cơ sở dữ liệu.

---

## Bước 11. Kiểm tra trạng thái

Sau khi tạo thành công:

```
Status

Available
```

Nếu trạng thái là **Available**, Database đã sẵn sàng sử dụng.

---

## Bước 12. Lấy Endpoint

Chọn Database vừa tạo.

Trong mục:

```
Connectivity & Security
```

Sao chép:

```
Endpoint
```

Ví dụ:

```
ai-supplychain-db.xxxxxxxxx.ap-southeast-1.rds.amazonaws.com
```

Endpoint này sẽ được sử dụng trong chương **5.4 Backend API Deployment** để Lambda kết nối tới PostgreSQL.

![Kiến trúc Amazon VPC](/images/5-Workshop/5.3-S3-vpc/5.3.4.png)


---

# Kiểm tra kết quả

Thông tin Database:

| Thuộc tính | Giá trị |
|------------|----------|
| Engine | PostgreSQL |
| Status | Available |
| Public Access | No |
| Storage | 20 GB |
| VPC | AI-SupplyChain-VPC |

---

# Best Practices

AWS khuyến nghị:

- Không bật Public Access cho Database.
- Đặt Database trong Private Subnets.
- Chỉ cho phép Lambda Security Group truy cập cổng 5432.
- Sử dụng Secrets Manager để lưu thông tin đăng nhập thay vì hard-code trong ứng dụng.
- Bật Automated Backups để tăng khả năng khôi phục dữ liệu.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công Amazon RDS PostgreSQL.
- Triển khai Database trong Private Subnets.
- Thiết lập Security Group bảo vệ Database.
- Thu được Endpoint để kết nối từ Backend.
- Chuẩn bị hạ tầng lưu trữ dữ liệu cho hệ thống AI Supply Chain Control Tower.