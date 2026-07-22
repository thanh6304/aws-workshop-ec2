---
title: "5.8.1 Kiểm thử xác thực người dùng với Amazon Cognito"
date: 2024-01-01
weight: 1
chapter: false
---



## Tổng quan

Trước khi người dùng có thể sử dụng AI Supply Chain Control Tower, họ phải được xác thực thông qua Amazon Cognito.

Trong bài này, bạn sẽ kiểm tra quy trình đăng nhập và xác minh rằng Amazon Cognito có thể cấp JSON Web Token (JWT) để truy cập Backend API.

---

## Kiến trúc

```text
User
   │
   ▼
Frontend (AWS Amplify)
   │
   ▼
Amazon Cognito
   │
JWT Access Token
   │
   ▼
API Gateway
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Đăng nhập bằng Amazon Cognito.
- Nhận JWT Access Token.
- Kiểm tra Token.
- Xác nhận API Gateway chấp nhận Token hợp lệ.

---

# Bước 1. Mở ứng dụng

Truy cập địa chỉ ứng dụng được triển khai bằng AWS Amplify.

Ví dụ:

```text
https://your-app.amplifyapp.com
```

Trang đăng nhập sẽ hiển thị.

---

# Bước 2. Đăng nhập

Nhập tài khoản đã tạo trong Cognito.

Ví dụ:

```text
Username

admin@example.com
```

```text
Password

********
```

Nhấn:

```text
Sign In
```

---

# Bước 3. Kiểm tra đăng nhập thành công

Sau khi đăng nhập thành công:

- Người dùng được chuyển đến Dashboard.
- Phiên đăng nhập được tạo.
- Amazon Cognito phát hành JWT Token.

---

# Bước 4. Kiểm tra JWT Token

Mở Developer Tools của trình duyệt.

Chọn:

```text
Application

Local Storage
```

Hoặc:

```text
Session Storage
```

Kiểm tra sự tồn tại của:

```text
Access Token

ID Token

Refresh Token
```

---

# Bước 5. Kiểm tra Access Token

Sao chép Access Token.

Có thể giải mã JWT bằng công cụ như JWT Decoder để kiểm tra các trường:

```text
sub

username

email

exp

iat
```

Đảm bảo Token chưa hết hạn và chứa đúng thông tin người dùng.

---

# Bước 6. Kiểm tra API Gateway

Thực hiện một yêu cầu đến Backend API với Header:

```text
Authorization:
Bearer <Access Token>
```

Nếu Token hợp lệ:

- API Gateway chuyển tiếp yêu cầu đến Lambda Backend.
- Lambda xử lý yêu cầu và trả về kết quả.

---

# Bước 7. Kiểm tra Token không hợp lệ

Thử:

- Xóa Token.
- Thay đổi một ký tự trong Token.
- Sử dụng Token đã hết hạn.

Kết quả mong đợi:

```text
401 Unauthorized
```

Điều này xác nhận API Gateway chỉ chấp nhận Token hợp lệ.

---

# Bước 8. Kiểm tra CloudWatch Logs

Mở:

```text
CloudWatch

Log Groups

/API-Gateway-Execution-Logs

/aws/lambda/backend-api
```

Đảm bảo:

- Không có lỗi xác thực.
- Các yêu cầu hợp lệ được ghi nhận thành công.

---

## Best Practices

- Luôn sử dụng HTTPS khi truyền JWT.
- Không lưu Token ở nơi công khai.
- Sử dụng Access Token trong thời gian ngắn và Refresh Token để cấp lại phiên.
- Kiểm tra thời gian hết hạn của Token trước khi gửi yêu cầu.

---

## Kiểm tra kết quả

Đảm bảo:

- Người dùng đăng nhập thành công.
- Amazon Cognito phát hành JWT.
- API Gateway xác thực Token thành công.
- Token không hợp lệ bị từ chối với mã 401.
- CloudWatch Logs ghi nhận các yêu cầu.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Kiểm tra thành công quy trình xác thực với Amazon Cognito.
- Xác nhận API Gateway chỉ chấp nhận JWT hợp lệ.
- Đảm bảo cơ chế xác thực hoạt động đúng trước khi kiểm thử Backend API.
