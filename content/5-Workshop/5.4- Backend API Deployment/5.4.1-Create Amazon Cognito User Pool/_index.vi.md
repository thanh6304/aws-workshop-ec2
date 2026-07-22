---
title: "5.4.1 Tạo Amazon Cognito User Pool"
date: 2024-01-01
weight: 1
chapter: false
---

# 5.4.1 Tạo Amazon Cognito User Pool

## Tổng quan

Amazon Cognito là dịch vụ quản lý danh tính và xác thực người dùng (Authentication & Authorization) của AWS.

Trong hệ thống **AI Supply Chain Control Tower**, Amazon Cognito chịu trách nhiệm quản lý tài khoản người dùng, thực hiện đăng nhập và cấp phát **JSON Web Token (JWT)**. Token này sẽ được gửi kèm trong mỗi yêu cầu đến Amazon API Gateway để xác minh danh tính trước khi truy cập các API của hệ thống.

Việc sử dụng Amazon Cognito giúp giảm đáng kể công sức xây dựng hệ thống đăng nhập, đồng thời đáp ứng các tiêu chuẩn bảo mật hiện đại.

---

# Kiến trúc

```
User
 │
 ▼
Amazon Cognito
 │
 │ JWT Token
 ▼
Amazon API Gateway
 │
 ▼
Lambda Backend API
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo Amazon Cognito User Pool.
- Cấu hình phương thức đăng nhập.
- Tạo App Client.
- Tạo tài khoản người dùng thử nghiệm.
- Lấy JWT Token để sử dụng trong các bài tiếp theo.

---

# Tại sao sử dụng Amazon Cognito?

Nếu tự xây dựng hệ thống đăng nhập, chúng ta phải xử lý:

- Lưu mật khẩu an toàn.
- Mã hóa thông tin đăng nhập.
- Quản lý phiên đăng nhập.
- Cấp phát Access Token.
- Đặt lại mật khẩu.
- Xác thực nhiều yếu tố (MFA).

Amazon Cognito cung cấp sẵn các tính năng trên và tích hợp chặt chẽ với các dịch vụ AWS khác.

---

# Các bước thực hiện

## Bước 1. Mở Amazon Cognito

Đăng nhập AWS Management Console.

Tìm kiếm:

```
Amazon Cognito
```

Chọn:

```
User Pools
```

![Amazon Cognito ](/images/5-Workshop/5.4-S3-onprem/5.3.4.png)

---

## Bước 2. Tạo User Pool

Nhấn:

```
Create User Pool
```

---

## Bước 3. Cấu hình đăng nhập

Chọn phương thức đăng nhập:

```
Email
```

Cho phép người dùng sử dụng địa chỉ email để đăng nhập.

> Chèn hình: Sign-in Options

---

## Bước 4. Thiết lập bảo mật

Giữ cấu hình mặc định:

- Password Policy: Default
- MFA: Optional hoặc Disabled (Workshop)
- Self Registration: Enabled

> Chèn hình: Security Configuration

---

## Bước 5. Cấu hình App Client

Trong phần **Application**:

| Thuộc tính       | Giá trị               |
| ---------------- | --------------------- |
| Application Type | Public Client         |
| App Client Name  | ai-supplychain-client |

Không tạo Client Secret để phù hợp với ứng dụng Web Frontend.

---

## Bước 6. Đặt tên User Pool

| Thuộc tính     | Giá trị                 |
| -------------- | ----------------------- |
| User Pool Name | ai-supplychain-userpool |

Nhấn:

```
Create User Pool
```

---

## Bước 7. Tạo người dùng

Chọn User Pool vừa tạo.

Vào:

```
Users

Create User
```

Tạo tài khoản thử nghiệm:

| Thuộc tính | Giá trị           |
| ---------- | ----------------- |
| Username   | admin@example.com |
| Email      | admin@example.com |
| Password   | Admin@123456      |

Người dùng sẽ được yêu cầu đổi mật khẩu trong lần đăng nhập đầu tiên nếu giữ mặc định.

---

## Bước 8. Ghi lại thông tin

Lưu các thông tin sau để sử dụng ở các bài tiếp theo:

- User Pool ID
- App Client ID
- AWS Region

Ví dụ:

```
Region

ap-southeast-1

---------------------

User Pool ID

ap-southeast-1_xxxxxxxxx

---------------------

Client ID

4sj9xxxxxxxxxxxxxxx
```

---

# Kiểm tra kết quả

Đảm bảo:

- User Pool được tạo thành công.
- App Client hiển thị trong mục Applications.
- Tài khoản người dùng xuất hiện trong danh sách Users.

---

# Best Practices

AWS khuyến nghị:

- Sử dụng Email hoặc Username làm định danh đăng nhập.
- Bật MFA trong môi trường Production.
- Thiết lập chính sách mật khẩu mạnh.
- Không chia sẻ App Client ID và User Pool ID công khai nếu không cần thiết.
- Sử dụng Hosted UI hoặc OAuth 2.0 khi xây dựng ứng dụng thực tế.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công Amazon Cognito User Pool.
- Tạo App Client.
- Tạo tài khoản người dùng.
- Chuẩn bị hệ thống xác thực bằng JWT cho các API ở những bài tiếp theo.
