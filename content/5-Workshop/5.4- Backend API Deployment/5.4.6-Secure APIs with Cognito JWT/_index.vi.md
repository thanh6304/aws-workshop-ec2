---
title: "5.4.6 Bảo vệ API bằng Amazon Cognito JWT"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.4.6 Bảo vệ API bằng Amazon Cognito JWT

## Tổng quan

Sau khi Backend API được triển khai, bước tiếp theo là bảo vệ các API khỏi những truy cập trái phép.

Trong hệ thống **AI Supply Chain Control Tower**, Amazon Cognito được sử dụng để xác thực người dùng và phát hành **JSON Web Token (JWT)** sau khi đăng nhập thành công.

Amazon API Gateway sẽ xác minh JWT trước khi chuyển yêu cầu đến Lambda Backend. Chỉ những yêu cầu có Access Token hợp lệ mới được xử lý.

Cơ chế này giúp tăng cường bảo mật, giảm tải cho Lambda và tuân thủ kiến trúc xác thực hiện đại.

---

# Kiến trúc

```
User
 │
 │ Login
 ▼
Amazon Cognito
 │
 │ JWT Token
 ▼
API Gateway (JWT Authorizer)
 │
 │ Validate Token
 ▼
Lambda Backend
 │
 ▼
Amazon RDS
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo JWT Authorizer.
- Kết nối API Gateway với Amazon Cognito.
- Bảo vệ các API riêng tư.
- Kiểm tra Access Token.
- Hiểu quy trình xác thực JWT.

---

# Quy trình xác thực

Quy trình hoạt động của hệ thống:

**Bước 1**

Người dùng đăng nhập thông qua Amazon Cognito.

↓

**Bước 2**

Amazon Cognito xác thực tài khoản.

↓

**Bước 3**

Cognito phát hành:

- ID Token
- Access Token
- Refresh Token

↓

**Bước 4**

Frontend gửi Access Token trong Header:

```
Authorization: Bearer <JWT Token>
```

↓

**Bước 5**

API Gateway xác minh JWT.

↓

**Bước 6**

Nếu hợp lệ:

```
Lambda được gọi.
```

Nếu không hợp lệ:

```
HTTP 401 Unauthorized
```

---

# Các bước thực hiện

## Bước 1. Mở API Gateway

Truy cập:

```
Amazon API Gateway
```

Chọn API:

```
AI-SupplyChain-API
```

---

## Bước 2. Tạo JWT Authorizer

Chọn:

```
Authorization

Create
```

Loại:

```
JWT
```

---

## Bước 3. Cấu hình Authorizer

| Thuộc tính      | Giá trị                                                       |
| --------------- | ------------------------------------------------------------- |
| Name            | CognitoAuthorizer                                             |
| Identity Source | Authorization Header                                          |
| Issuer URL      | https://cognito-idp.ap-southeast-1.amazonaws.com/<UserPoolId> |
| Audience        | App Client ID                                                 |

![API Gateway Dashboard](/images/5-Workshop/5.4-S3-onprem/5.4.4.png)

---

## Bước 4. Gắn Authorizer vào Route

Áp dụng JWT Authorizer cho các API:

| Route            |
| ---------------- |
| GET /products    |
| POST /products   |
| GET /orders      |
| POST /orders     |
| POST /ai/predict |

Không áp dụng cho:

```
GET /health
```

để phục vụ kiểm tra trạng thái hệ thống.

---

## Bước 5. Triển khai lại API

Sau khi lưu thay đổi:

```
Deploy
```

API Gateway sẽ cập nhật cấu hình Authorizer.

---

## Bước 6. Đăng nhập lấy JWT

Đăng nhập bằng tài khoản đã tạo ở mục **5.4.1**.

Sau khi xác thực thành công, Cognito sẽ trả về:

```json
{
  "AccessToken": "eyJraWQiOi...",
  "IdToken": "eyJraWQiOi...",
  "RefreshToken": "eyJjdHkiOi..."
}
```

---

## Bước 7. Gọi API

Trong Postman:

```
Authorization

Bearer Token
```

Dán Access Token.

Ví dụ:

```
GET

/products
```

Nếu Token hợp lệ:

```
HTTP 200 OK
```

Nếu Token không hợp lệ:

```
HTTP 401 Unauthorized
```

---

# Kiểm tra kết quả

Đảm bảo:

- JWT Authorizer được tạo.
- API Gateway xác minh JWT thành công.
- API trả về dữ liệu khi Token hợp lệ.
- API từ chối truy cập khi Token không hợp lệ.

---

# Best Practices

AWS khuyến nghị:

- Không lưu JWT Token trong Local Storage đối với các ứng dụng yêu cầu bảo mật cao.
- Luôn truyền Token qua HTTPS.
- Thiết lập thời gian hết hạn phù hợp.
- Sử dụng Refresh Token để cấp lại Access Token.
- Chỉ bảo vệ các API cần xác thực, giữ các endpoint kiểm tra sức khỏe (health check) ở chế độ công khai nếu phù hợp.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Cấu hình thành công JWT Authorizer.
- Kết nối Amazon Cognito với Amazon API Gateway.
- Bảo vệ các Backend API bằng JWT.
- Hoàn thiện cơ chế xác thực của hệ thống AI Supply Chain Control Tower.
