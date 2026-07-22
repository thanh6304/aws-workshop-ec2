---
title: "5.7.8 Kiểm tra và xác thực Monitoring & Security"
date: 2024-01-01
weight: 8
chapter: false
---



## Tổng quan

Sau khi triển khai các dịch vụ giám sát và bảo mật, bước cuối cùng là xác thực toàn bộ cấu hình để đảm bảo hệ thống hoạt động đúng như mong đợi.

Trong bài này, bạn sẽ kiểm tra:

- Amazon CloudWatch
- CloudWatch Alarms
- Amazon SNS
- AWS IAM
- AWS Secrets Manager
- AWS KMS
- AWS CloudTrail

Sau khi hoàn thành, hệ thống AI Supply Chain Control Tower sẽ sẵn sàng để vận hành và chuyển sang giai đoạn kiểm thử toàn diện.

---

## Kiến trúc

```text
                   AI Supply Chain Control Tower
                              │
      ┌───────────────┬────────┴─────────┬───────────────┐
      ▼               ▼                  ▼               ▼
 CloudWatch       CloudTrail      Secrets Manager      AWS KMS
      │               │                  │               │
      ▼               ▼                  ▼               ▼
 CloudWatch      Audit Logs        Database Secret   Encryption
   Alarms
      │
      ▼
 Amazon SNS
      │
      ▼
 Administrator
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Kiểm tra CloudWatch Dashboard.
- Kiểm tra CloudWatch Alarms.
- Kiểm tra SNS Notification.
- Kiểm tra IAM Roles.
- Kiểm tra Secrets Manager.
- Kiểm tra KMS Encryption.
- Kiểm tra CloudTrail Logs.

---

# Bước 1. Kiểm tra CloudWatch Dashboard

Mở:

```text
Amazon CloudWatch

Dashboards
```

Đảm bảo Dashboard:

```text
SupplyChainMonitoring
```

hiển thị đầy đủ các Widget:

```text
Lambda Duration

Lambda Errors

API Gateway Latency

RDS CPU

SQS Queue Depth

Athena Query Count
```

---

# Bước 2. Kiểm tra CloudWatch Alarms

Mở:

```text
CloudWatch

Alarms
```

Đảm bảo các Alarm tồn tại:

```text
BackendLambdaErrors

BackendLambdaDuration

ApiGateway5XXErrors

RDSHighCPU

SQSQueueDepth

AthenaFailedQueries
```

Trạng thái:

```text
OK
```

---

# Bước 3. Kiểm tra Amazon SNS

Mở:

```text
Amazon SNS
```

Kiểm tra:

```text
Topic

SupplyChainAlerts
```

Subscription:

```text
Confirmed
```

Thực hiện kiểm tra bằng cách kích hoạt một Alarm và xác nhận email được gửi đến người quản trị.

---

# Bước 4. Kiểm tra IAM

Mở:

```text
IAM
```

Kiểm tra:

```text
Users

SupplyChainAdmin
```

Kiểm tra các Role:

```text
BackendLambdaRole

AIWorkerLambdaRole
```

Đảm bảo các Role được gán đúng Policy và chỉ có các quyền cần thiết.

---

# Bước 5. Kiểm tra AWS Secrets Manager

Mở:

```text
Secrets Manager
```

Đảm bảo Secret:

```text
SupplyChainDatabaseSecret
```

tồn tại và Backend Lambda có thể truy xuất thông tin kết nối cơ sở dữ liệu mà không phát sinh lỗi.

---

# Bước 6. Kiểm tra AWS KMS

Mở:

```text
AWS KMS
```

Kiểm tra:

```text
alias/supplychain-key
```

Xác nhận:

- Key Rotation được bật (nếu áp dụng).
- IAM Roles được phép sử dụng khóa.
- Các dịch vụ AWS sử dụng đúng Customer Managed Key.

---

# Bước 7. Kiểm tra AWS CloudTrail

Mở:

```text
CloudTrail

Event History
```

Xác nhận các sự kiện:

```text
InvokeFunction

PutObject

StartQueryExecution

GetSecretValue

SendMessage
```

Đảm bảo Event History ghi nhận đầy đủ các thao tác đã thực hiện.

---

# Bước 8. Kiểm tra CloudWatch Logs

Mở:

```text
CloudWatch

Log Groups
```

Kiểm tra:

```text
/aws/lambda/backend-api

/aws/lambda/ai-worker

/aws/cloudtrail/supplychain
```

Đảm bảo không có lỗi nghiêm trọng như:

```text
AccessDeniedException

Timeout

Unhandled Exception
```

---

# Bước 9. Kiểm tra mã hóa dữ liệu

Xác nhận:

```text
Amazon S3

AWS KMS Encryption Enabled
```

```text
Amazon RDS

Storage Encryption Enabled
```

```text
Secrets Manager

Customer Managed Key Enabled
```

---

## Checklist

| Thành phần           | Trạng thái |
| -------------------- | ---------- |
| CloudWatch Dashboard | ✅         |
| CloudWatch Alarms    | ✅         |
| Amazon SNS           | ✅         |
| IAM Users            | ✅         |
| IAM Roles            | ✅         |
| Secrets Manager      | ✅         |
| AWS KMS              | ✅         |
| CloudTrail           | ✅         |
| CloudWatch Logs      | ✅         |

---

## Best Practices

- Theo dõi Dashboard hằng ngày.
- Kiểm tra Alarm History định kỳ.
- Định kỳ rà soát IAM Policies.
- Bật Key Rotation cho Customer Managed Keys.
- Kiểm tra CloudTrail Logs để phát hiện các hoạt động bất thường.
- Không lưu thông tin nhạy cảm trong mã nguồn hoặc tệp cấu hình.

---

## Kiểm tra kết quả

Đảm bảo:

- Dashboard hiển thị đầy đủ Metrics.
- Alarm hoạt động bình thường.
- SNS gửi được email thông báo.
- Lambda sử dụng đúng IAM Role.
- Database Credentials được truy xuất từ Secrets Manager.
- Dữ liệu được mã hóa bằng AWS KMS.
- CloudTrail ghi nhận các API Calls.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Hoàn thiện hệ thống giám sát với Amazon CloudWatch.
- Thiết lập cảnh báo tự động bằng Amazon SNS.
- Áp dụng nguyên tắc Least Privilege với AWS IAM.
- Bảo vệ thông tin nhạy cảm bằng AWS Secrets Manager.
- Mã hóa dữ liệu bằng AWS KMS.
- Triển khai kiểm toán với AWS CloudTrail.
- Xác thực toàn bộ Monitoring & Security cho AI Supply Chain Control Tower.
