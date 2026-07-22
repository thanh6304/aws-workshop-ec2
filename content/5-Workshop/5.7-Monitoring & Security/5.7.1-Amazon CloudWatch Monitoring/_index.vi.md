---
title: "5.7.1 Giám sát hệ thống với Amazon CloudWatch"
date: 2024-01-01
weight: 1
chapter: false
---


## Tổng quan

Sau khi triển khai hệ thống AI Supply Chain Control Tower trên AWS, việc giám sát tài nguyên và ứng dụng là rất quan trọng để đảm bảo hệ thống luôn hoạt động ổn định.

Amazon CloudWatch là dịch vụ giám sát của AWS, cho phép thu thập:

- Metrics
- Logs
- Events
- Dashboards
- Alarms

CloudWatch giúp theo dõi hiệu năng của:

- AWS Lambda
- Amazon RDS
- Amazon SQS
- Amazon Athena
- Amazon Bedrock
- API Gateway

Trong workshop này, CloudWatch sẽ được sử dụng để giám sát toàn bộ hệ thống.

---

## Kiến trúc

```text
Application
        │
        ▼
AWS Services
        │
        ├── Lambda
        ├── API Gateway
        ├── RDS
        ├── SQS
        ├── Athena
        └── Bedrock
                │
                ▼
        Amazon CloudWatch
                │
        ┌───────┴────────┐
        ▼                ▼
   Metrics           Log Groups
        │                │
        └───────┬────────┘
                ▼
         Dashboard / Alarm
```

---

## Mục tiêu

Sau bài này bạn sẽ:

- Mở Amazon CloudWatch.
- Kiểm tra Metrics.
- Kiểm tra Logs.
- Theo dõi Lambda.
- Theo dõi API Gateway.
- Theo dõi RDS.
- Theo dõi SQS.
- Chuẩn bị cho CloudWatch Alarm.

---

# Bước 1. Mở Amazon CloudWatch

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
CloudWatch
```

Chọn:

```text
Amazon CloudWatch
```

---

# Bước 2. Khám phá giao diện

Trong thanh điều hướng bên trái, bạn sẽ thấy:

```text
Dashboards

Metrics

Alarms

Logs

Insights

Application Signals
```

Trong workshop này chúng ta tập trung vào:

```text
Metrics

Logs

Dashboards
```

---

# Bước 3. Kiểm tra Lambda Metrics

Chọn:

```text
Metrics
```

Tiếp theo:

```text
AWS/Lambda
```

Chọn Lambda:

```text
Backend API

AI Worker
```

Các chỉ số quan trọng:

```text
Invocations

Duration

Errors

Concurrent Executions

Throttles
```

---

# Bước 4. Kiểm tra API Gateway

Chọn:

```text
AWS/ApiGateway
```

Theo dõi:

```text
Count

Latency

4XXError

5XXError

IntegrationLatency
```

Các chỉ số này giúp phát hiện lỗi từ phía người dùng hoặc backend.

---

# Bước 5. Kiểm tra Amazon RDS

Chọn:

```text
AWS/RDS
```

Kiểm tra:

```text
CPU Utilization

Database Connections

Free Storage Space

Read IOPS

Write IOPS
```

Nếu CPU hoặc số lượng kết nối tăng bất thường, cần kiểm tra ứng dụng hoặc tối ưu truy vấn.

---

# Bước 6. Kiểm tra Amazon SQS

Chọn:

```text
AWS/SQS
```

Theo dõi:

```text
ApproximateNumberOfMessagesVisible

ApproximateAgeOfOldestMessage

NumberOfMessagesSent

NumberOfMessagesReceived
```

Nếu số lượng tin nhắn tăng liên tục, AI Worker có thể đang xử lý chậm hoặc gặp lỗi.

---

# Bước 7. Kiểm tra Amazon Athena

Chọn:

```text
AWS/Athena
```

Theo dõi:

```text
SuccessfulQueries

FailedQueries

QueryRuntime
```

Đảm bảo các truy vấn phân tích hoàn thành thành công và thời gian thực thi nằm trong giới hạn mong muốn.

---

# Bước 8. Kiểm tra Log Groups

Chọn:

```text
Logs

Log Groups
```

Bạn sẽ thấy các Log Group tương tự:

```text
/aws/lambda/backend-api

/aws/lambda/ai-worker

/API-Gateway-Execution-Logs
```

Chọn một Log Group để xem:

```text
Log Streams

Timestamp

Request ID

Execution Details

Error Messages
```

---

# Bước 9. Kiểm tra Log của Lambda

Mở:

```text
/aws/lambda/ai-worker
```

Chọn Log Stream mới nhất.

Ví dụ:

```text
START RequestId

Processing SQS Message

Calling Amazon Athena

Generating AI Summary

END RequestId

REPORT Duration: 320 ms
```

Nếu có lỗi, CloudWatch sẽ hiển thị Stack Trace và thông tin chi tiết để hỗ trợ xử lý.

---

# Bước 10. Tạo Dashboard giám sát

Trong CloudWatch, chọn:

```text
Dashboards
```

Chọn:

```text
Create dashboard
```

Đặt tên:

```text
SupplyChainMonitoring
```

Thêm các Widget:

```text
Lambda Duration

Lambda Errors

API Gateway Latency

RDS CPU

SQS Queue Depth

Athena Query Count
```

Dashboard giúp theo dõi toàn bộ hệ thống trên một màn hình duy nhất.

---

## Best Practices

- Theo dõi Metrics và Logs thường xuyên.
- Đặt tên Dashboard và Log Group theo quy ước thống nhất.
- Sử dụng Dashboard để giám sát các thành phần quan trọng.
- Lưu ý các chỉ số Duration, Errors và Queue Depth để phát hiện sớm sự cố.
- Chỉ cấp quyền CloudWatch cho các IAM Role thực sự cần thiết.

---

## Kiểm tra kết quả

Đảm bảo:

- CloudWatch hiển thị Metrics của Lambda, API Gateway, RDS và SQS.
- Log Group của Backend API và AI Worker tồn tại.
- Có thể xem Log Stream mới nhất.
- Dashboard `SupplyChainMonitoring` được tạo.
- Các biểu đồ hiển thị dữ liệu chính xác.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Làm quen với Amazon CloudWatch.
- Theo dõi Metrics của các dịch vụ AWS.
- Kiểm tra Logs của Lambda và API Gateway.
- Xây dựng Dashboard giám sát hệ thống.
- Chuẩn bị cho bước cấu hình CloudWatch Alarms.
