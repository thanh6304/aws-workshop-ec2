---
title: "5.7.4 Bảo mật quyền truy cập với AWS Identity and Access Management (IAM)"
date: 2024-01-01
weight: 4
chapter: false
---


## Tổng quan

AWS Identity and Access Management (IAM) cho phép bạn kiểm soát quyền truy cập vào các tài nguyên AWS.

Thay vì cấp toàn bộ quyền **AdministratorAccess**, IAM cho phép tạo các chính sách (Policies) chỉ cấp đúng quyền mà người dùng hoặc dịch vụ cần sử dụng.

Trong workshop này, IAM được sử dụng để:

- Quản lý người dùng quản trị.
- Cấp quyền cho Lambda Backend.
- Cấp quyền cho AI Worker.
- Quản lý quyền truy cập Amazon S3, Amazon SQS, Amazon Athena và Amazon Bedrock.
- Áp dụng nguyên tắc **Least Privilege**.

---

## Kiến trúc

```text
IAM Users
      │
      ▼
IAM Policies
      │
      ▼
IAM Roles
      │
      ├── Backend Lambda
      ├── AI Worker Lambda
      └── Administrator
              │
              ▼
AWS Resources

S3
SQS
Athena
Bedrock
Secrets Manager
CloudWatch
RDS
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Tạo IAM User.
- Tạo IAM Policy.
- Tạo IAM Role.
- Gán Role cho Lambda.
- Kiểm tra quyền truy cập.

---

# Bước 1. Mở IAM Console

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
IAM
```

Chọn:

```text
Identity and Access Management (IAM)
```

---

# Bước 2. Tạo IAM User

Chọn:

```text
Users

Create User
```

Tên:

```text
SupplyChainAdmin
```

Chọn:

```text
Provide user access to the AWS Management Console
```

Thiết lập mật khẩu theo yêu cầu của tổ chức hoặc sử dụng tùy chọn tự động tạo mật khẩu.

---

# Bước 3. Tạo IAM Policy cho Backend Lambda

Chọn:

```text
Policies

Create policy
```

Ví dụ chính sách:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["logs:*"],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::ai-supplychain-datalake/*"
    },
    {
      "Effect": "Allow",
      "Action": ["sqs:SendMessage"],
      "Resource": "*"
    }
  ]
}
```

Tên Policy:

```text
BackendLambdaPolicy
```

---

# Bước 4. Tạo IAM Policy cho AI Worker

Ví dụ:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["athena:*", "bedrock:InvokeModel", "s3:GetObject", "logs:*"],
      "Resource": "*"
    }
  ]
}
```

Tên:

```text
AIWorkerPolicy
```

---

# Bước 5. Tạo IAM Role cho Lambda Backend

Chọn:

```text
Roles

Create role
```

Trusted entity:

```text
AWS Service
```

Use case:

```text
Lambda
```

Gắn:

```text
BackendLambdaPolicy
```

Đặt tên:

```text
BackendLambdaRole
```

---

# Bước 6. Tạo IAM Role cho AI Worker

Tương tự, tạo Role:

```text
AIWorkerLambdaRole
```

Gắn:

```text
AIWorkerPolicy
```

---

# Bước 7. Gán Role cho Lambda

Mở:

```text
AWS Lambda
```

Chọn:

```text
backend-api
```

Trong phần:

```text
Configuration

Permissions
```

Đổi Execution Role thành:

```text
BackendLambdaRole
```

Lặp lại với:

```text
ai-worker
```

Sử dụng:

```text
AIWorkerLambdaRole
```

---

# Bước 8. Kiểm tra quyền truy cập

Thực hiện một yêu cầu tới Backend API hoặc AI Worker.

Xác nhận:

- Lambda ghi log vào CloudWatch.
- Backend có thể gửi tin nhắn tới Amazon SQS.
- AI Worker đọc dữ liệu từ Amazon S3.
- AI Worker thực thi truy vấn Amazon Athena.
- AI Worker gọi Amazon Bedrock thành công.

Nếu thiếu quyền, Lambda sẽ ghi lỗi **AccessDeniedException** trong CloudWatch Logs.

---

## Best Practices

- Áp dụng nguyên tắc **Least Privilege**.
- Không sử dụng `AdministratorAccess` cho Lambda.
- Tách riêng IAM Role cho từng chức năng.
- Định kỳ rà soát và cập nhật IAM Policies.
- Theo dõi các lỗi truy cập bằng CloudWatch Logs và AWS CloudTrail.

---

## Kiểm tra kết quả

Đảm bảo:

- IAM User được tạo.
- IAM Policies được tạo và gắn đúng.
- IAM Roles được gán cho các Lambda.
- Lambda truy cập được các dịch vụ AWS cần thiết.
- Không xuất hiện lỗi **AccessDeniedException**.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo IAM User phục vụ quản trị.
- Xây dựng IAM Policies theo nguyên tắc Least Privilege.
- Tạo IAM Roles cho Backend và AI Worker.
- Cấu hình quyền truy cập an toàn cho các dịch vụ AWS.
