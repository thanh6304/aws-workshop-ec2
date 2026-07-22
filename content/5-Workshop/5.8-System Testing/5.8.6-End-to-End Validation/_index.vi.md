---
title: "5.8.6 Kiểm thử End-to-End"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.8.6 Kiểm thử End-to-End

## Tổng quan

Đây là bước cuối cùng của quá trình kiểm thử hệ thống.

Mục tiêu của bài này là xác nhận toàn bộ AI Supply Chain Control Tower hoạt động đúng từ giao diện người dùng đến các dịch vụ AWS phía sau.

Thay vì kiểm tra từng thành phần riêng lẻ, bạn sẽ thực hiện một quy trình nghiệp vụ hoàn chỉnh để đảm bảo dữ liệu được xử lý xuyên suốt toàn hệ thống.

---

## Kiến trúc

```text
User
   │
   ▼
AWS Amplify
   │
   ▼
Amazon Cognito
   │
JWT
   ▼
API Gateway
   │
   ▼
Backend Lambda
   │
   ▼
Amazon RDS
   │
   ▼
Amazon SQS
   │
   ▼
AI Worker Lambda
   │
   ▼
Amazon Athena
   │
   ▼
Amazon Bedrock
   │
   ▼
Amazon RDS (AI Insights)
   │
   ▼
Dashboard
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Kiểm thử toàn bộ quy trình nghiệp vụ.
- Xác minh luồng dữ liệu giữa các dịch vụ AWS.
- Kiểm tra Dashboard sau khi AI hoàn tất xử lý.
- Xác nhận hệ thống sẵn sàng triển khai thực tế.

---

# Bước 1. Đăng nhập hệ thống

Mở ứng dụng được triển khai trên AWS Amplify.

Đăng nhập bằng tài khoản Amazon Cognito.

Đảm bảo:

```text
Login Successful
```

Dashboard được hiển thị.

---

# Bước 2. Tạo đơn hàng mới

Tạo một đơn hàng mới từ giao diện người dùng hoặc thông qua Backend API.

Ví dụ:

```json
{
  "customerName": "John Smith",
  "productId": 101,
  "quantity": 20
}
```

Đảm bảo yêu cầu được xử lý thành công.

---

# Bước 3. Kiểm tra Backend

Xác nhận:

- API Gateway nhận yêu cầu.
- Backend Lambda xử lý thành công.
- Đơn hàng được lưu vào Amazon RDS.

Thực hiện truy vấn:

```sql
SELECT *
FROM orders
ORDER BY created_at DESC;
```

Bản ghi mới phải xuất hiện trong kết quả.

---

# Bước 4. Kiểm tra AI Pipeline

Sau khi đơn hàng được lưu:

```text
Backend Lambda

↓

Amazon SQS

↓

AI Worker Lambda

↓

Amazon Athena

↓

Amazon Bedrock
```

Đảm bảo toàn bộ Pipeline hoàn thành mà không phát sinh lỗi.

---

# Bước 5. Kiểm tra AI Insights

Thực hiện truy vấn:

```sql
SELECT *
FROM ai_insights
ORDER BY created_at DESC;
```

Đảm bảo có bản ghi mới với kết quả phân tích AI.

Ví dụ:

```text
High demand predicted for Product 101.

Recommended inventory replenishment within 3 days.
```

---

# Bước 6. Kiểm tra Dashboard

Làm mới Dashboard.

Xác nhận:

- KPI được cập nhật.
- Đơn hàng mới xuất hiện.
- AI Insights hiển thị kết quả mới.
- Không có lỗi giao diện.

---

# Bước 7. Kiểm tra Monitoring

Mở:

```text
CloudWatch Dashboard
```

Kiểm tra:

```text
Lambda Invocations

API Gateway Requests

SQS Messages

Athena Queries
```

Đảm bảo các Metrics phản ánh đúng hoạt động vừa thực hiện.

---

# Bước 8. Kiểm tra Security

Xác nhận:

- Người dùng được xác thực bởi Amazon Cognito.
- Lambda sử dụng IAM Roles phù hợp.
- Database Credentials được lấy từ AWS Secrets Manager.
- Dữ liệu được mã hóa bằng AWS KMS.

---

# Bước 9. Kiểm tra Audit Logs

Mở:

```text
AWS CloudTrail

Event History
```

Xác nhận xuất hiện các sự kiện như:

```text
InvokeFunction

PutObject

StartQueryExecution

GetSecretValue

InvokeModel

SendMessage
```

Điều này xác nhận toàn bộ hoạt động của hệ thống đã được ghi nhận.

---

## Checklist

| Thành phần          | Trạng thái |
| ------------------- | ---------- |
| Amazon Cognito      | ✅         |
| API Gateway         | ✅         |
| Backend Lambda      | ✅         |
| Amazon RDS          | ✅         |
| Amazon SQS          | ✅         |
| AI Worker Lambda    | ✅         |
| Amazon Athena       | ✅         |
| Amazon Bedrock      | ✅         |
| Dashboard           | ✅         |
| CloudWatch          | ✅         |
| Amazon SNS          | ✅         |
| AWS IAM             | ✅         |
| AWS Secrets Manager | ✅         |
| AWS KMS             | ✅         |
| AWS CloudTrail      | ✅         |

---

## Best Practices

- Thực hiện kiểm thử End-to-End sau mỗi lần triển khai lớn.
- Chuẩn bị bộ dữ liệu kiểm thử đại diện cho các tình huống thực tế.
- Kiểm tra cả trường hợp thành công và thất bại.
- Theo dõi Metrics và Logs trong suốt quá trình kiểm thử.
- Tự động hóa các bài kiểm thử End-to-End trong quy trình CI/CD nếu có thể.

---

## Kiểm tra kết quả

Đảm bảo:

- Người dùng đăng nhập thành công.
- Backend xử lý đúng yêu cầu.
- Amazon RDS lưu dữ liệu.
- Amazon SQS kích hoạt AI Worker.
- Amazon Athena thực hiện truy vấn.
- Amazon Bedrock tạo kết quả phân tích.
- Dashboard hiển thị dữ liệu và AI Insights mới.
- CloudWatch và CloudTrail ghi nhận đầy đủ hoạt động.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Kiểm thử thành công toàn bộ AI Supply Chain Control Tower.
- Xác nhận sự tích hợp giữa các dịch vụ AWS.
- Đảm bảo dữ liệu được xử lý xuyên suốt từ Frontend đến AI Pipeline.
- Hoàn tất quá trình kiểm thử trước khi dọn dẹp tài nguyên AWS.
