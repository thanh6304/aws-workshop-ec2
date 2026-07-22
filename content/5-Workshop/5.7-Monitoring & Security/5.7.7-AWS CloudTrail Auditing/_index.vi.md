---
title: "5.7.7 Kiểm toán tài nguyên AWS với AWS CloudTrail"
date: 2024-01-01
weight: 7
chapter: false
---

# 5.7.7 Kiểm toán tài nguyên AWS với AWS CloudTrail

## Tổng quan

AWS CloudTrail là dịch vụ ghi nhận lịch sử hoạt động của tài khoản AWS bằng cách lưu lại các API call được thực hiện thông qua:

- AWS Management Console
- AWS CLI
- AWS SDK
- Các dịch vụ AWS

CloudTrail giúp quản trị viên:

- Theo dõi hoạt động của người dùng.
- Kiểm tra thay đổi cấu hình.
- Điều tra sự cố bảo mật.
- Đáp ứng yêu cầu kiểm toán và tuân thủ.

Trong workshop này, CloudTrail sẽ ghi nhận các hoạt động liên quan đến AI Supply Chain Control Tower.

---

## Kiến trúc

```text
Users / Applications
          │
          ▼
      AWS API Calls
          │
          ▼
     AWS CloudTrail
          │
      ┌───┴──────────┐
      ▼              ▼
 Amazon S3      CloudWatch Logs
      │              │
      ▼              ▼
 Audit Reports   Monitoring
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Bật AWS CloudTrail.
- Tạo Trail mới.
- Lưu nhật ký vào Amazon S3.
- Theo dõi các API Events.
- Tìm kiếm lịch sử hoạt động.

---

# Bước 1. Mở AWS CloudTrail

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
CloudTrail
```

Chọn:

```text
AWS CloudTrail
```

---

# Bước 2. Tạo Trail

Chọn:

```text
Trails

Create trail
```

Thiết lập:

| Thuộc tính                 | Giá trị               |
| -------------------------- | --------------------- |
| Trail Name                 | SupplyChainAuditTrail |
| Apply trail to all Regions | Yes                   |
| Enable log file validation | Yes                   |

---

# Bước 3. Chọn nơi lưu nhật ký

Chọn:

```text
Create new S3 bucket
```

Tên Bucket:

```text
ai-supplychain-cloudtrail
```

Hoặc sử dụng Bucket đã tạo:

```text
ai-supplychain-datalake
```

với thư mục:

```text
cloudtrail/
```

CloudTrail sẽ tự động lưu các tệp nhật ký theo thời gian.

---

# Bước 4. Bật CloudWatch Logs Integration

Chọn:

```text
Send logs to CloudWatch Logs
```

Log Group:

```text
/aws/cloudtrail/supplychain
```

IAM Role:

```text
CloudTrail_CloudWatchLogs_Role
```

Việc tích hợp với CloudWatch giúp tìm kiếm và phân tích log dễ dàng hơn.

---

# Bước 5. Cấu hình Event Types

Chọn ghi nhận:

```text
Management Events
```

Bao gồm:

- Read events
- Write events

Đối với workshop này, chỉ cần Management Events là đủ để theo dõi các thao tác quản trị.

---

# Bước 6. Thực hiện một số thao tác kiểm tra

Sau khi Trail hoạt động, thực hiện:

- Mở Lambda.
- Xem thông tin RDS.
- Tải một tệp lên Amazon S3.
- Chạy một truy vấn Athena.

Những thao tác này sẽ được CloudTrail ghi nhận.

---

# Bước 7. Xem Event History

Trong CloudTrail, chọn:

```text
Event history
```

Quan sát các sự kiện:

| Event Name          | Service             |
| ------------------- | ------------------- |
| InvokeFunction      | AWS Lambda          |
| PutObject           | Amazon S3           |
| StartQueryExecution | Amazon Athena       |
| GetSecretValue      | AWS Secrets Manager |
| SendMessage         | Amazon SQS          |

---

# Bước 8. Kiểm tra chi tiết Event

Chọn một sự kiện.

Quan sát:

```text
Event Time

Username

AWS Service

Source IP

Region

Request Parameters

Response Elements
```

Thông tin này hỗ trợ điều tra sự cố và kiểm toán.

---

# Bước 9. Kiểm tra tệp nhật ký

Mở Bucket:

```text
ai-supplychain-cloudtrail
```

Hoặc:

```text
ai-supplychain-datalake/cloudtrail/
```

Kiểm tra các tệp log dạng JSON được CloudTrail tạo.

---

## Best Practices

- Bật CloudTrail cho tất cả AWS Regions.
- Kích hoạt Log File Validation.
- Lưu log vào Amazon S3 và tích hợp với CloudWatch Logs.
- Hạn chế quyền truy cập vào Bucket chứa CloudTrail Logs.
- Theo dõi các API nhạy cảm như IAM, KMS và Secrets Manager.

---

## Kiểm tra kết quả

Đảm bảo:

- Trail được tạo thành công.
- Event History hiển thị các hoạt động gần đây.
- Log được lưu vào Amazon S3.
- CloudWatch Logs nhận được sự kiện.
- Có thể tra cứu các API Call theo thời gian.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Triển khai AWS CloudTrail để ghi nhận hoạt động tài khoản.
- Lưu trữ nhật ký kiểm toán trong Amazon S3.
- Theo dõi các API Call của hệ thống.
- Tăng cường khả năng kiểm toán và điều tra sự cố.
