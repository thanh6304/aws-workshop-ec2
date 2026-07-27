---
title: "5.7.2 Cấu hình CloudWatch Alarms"
date: 2024-01-01
weight: 2
chapter: false
---

## Tổng quan

Amazon CloudWatch Alarms liên tục giám sát các chỉ số (Metrics) của các dịch vụ AWS và phát hiện các điều kiện hoạt động bất thường.

Khi một chỉ số vượt quá ngưỡng (Threshold) đã được thiết lập, Alarm sẽ chuyển sang trạng thái **ALARM**. Ở phần tiếp theo, bạn sẽ cấu hình Amazon SNS để tự động gửi thông báo cho quản trị viên khi Alarm được kích hoạt.

Trong bài thực hành này, bạn sẽ tạo CloudWatch Alarm cho các dịch vụ sau:

- AWS Lambda
- Amazon API Gateway
- Amazon RDS
- Amazon SQS
- Amazon Athena

---

## Kiến trúc

```text
Các dịch vụ AWS
      │
      ├── Lambda
      ├── API Gateway
      ├── Amazon RDS
      ├── Amazon SQS
      └── Amazon Athena
               │
               ▼
      Amazon CloudWatch
               │
      CloudWatch Alarms
               │
      Thay đổi trạng thái Alarm
               │
               ▼
      Amazon SNS (Phần tiếp theo)
```

---

## Mục tiêu

Sau khi hoàn thành phần này, bạn sẽ có thể:

- Tạo CloudWatch Alarm.
- Cấu hình ngưỡng cảnh báo (Threshold).
- Theo dõi trạng thái của Alarm.
- Chuẩn bị tích hợp Amazon SNS.

---

# Bước 1. Mở CloudWatch Alarms

Truy cập **AWS Management Console**.

Điều hướng đến:

```text
Amazon CloudWatch
```

Chọn:

```text
Alarms
```

Sau đó nhấn:

```text
Create alarm
```

---

# Bước 2. Tạo Alarm cho lỗi Lambda

Chọn Metric:

```text
AWS/Lambda

Errors
```

Chọn Lambda Function:

```text
backend-api
```

Cấu hình:

| Thuộc tính | Giá trị |
|------------|----------|
| Statistic | Sum |
| Period | 5 Minutes |
| Threshold | Greater than 0 |

Đặt tên Alarm:

```text
BackendLambdaErrors
```

---

# Bước 3. Tạo Alarm cho thời gian thực thi Lambda

Chọn Metric:

```text
Duration
```

Cấu hình:

| Thuộc tính | Giá trị |
|------------|----------|
| Statistic | Average |
| Threshold | 5000 ms |

Đặt tên Alarm:

```text
BackendLambdaDuration
```

---

# Bước 4. Tạo Alarm cho API Gateway

Chọn Metric:

```text
5XXError
```

Cấu hình:

| Thuộc tính | Giá trị |
|------------|----------|
| Statistic | Sum |
| Threshold | Greater than 0 |

Đặt tên Alarm:

```text
ApiGateway5XXErrors
```

---

# Bước 5. Tạo Alarm cho Amazon RDS

Chọn Metric:

```text
CPUUtilization
```

Cấu hình:

| Thuộc tính | Giá trị |
|------------|----------|
| Statistic | Average |
| Threshold | 80% |

Đặt tên Alarm:

```text
RDSHighCPU
```

---

# Bước 6. Tạo Alarm cho Amazon SQS

Chọn Metric:

```text
ApproximateNumberOfMessagesVisible
```

Cấu hình:

| Thuộc tính | Giá trị |
|------------|----------|
| Statistic | Average |
| Threshold | Greater than 100 |

Đặt tên Alarm:

```text
SQSQueueDepth
```

---

# Bước 7. Tạo Alarm cho Amazon Athena

Chọn Metric:

```text
FailedQueries
```

Cấu hình:

| Thuộc tính | Giá trị |
|------------|----------|
| Statistic | Sum |
| Threshold | Greater than 0 |

Đặt tên Alarm:

```text
AthenaFailedQueries
```

---

# Bước 8. Kiểm tra trạng thái Alarm

Điều hướng đến:

```text
CloudWatch → Alarms
```

Ví dụ:

| Alarm | Trạng thái |
|------------------------|------------|
| BackendLambdaErrors | OK |
| BackendLambdaDuration | OK |
| ApiGateway5XXErrors | OK |
| RDSHighCPU | OK |
| SQSQueueDepth | OK |
| AthenaFailedQueries | OK |

Khi một chỉ số vượt quá ngưỡng đã thiết lập, trạng thái sẽ chuyển thành:

```text
ALARM
```

---

# Bước 9. Xem lịch sử Alarm

Mở bất kỳ Alarm nào và chọn tab **History** để xem:

- Thời gian tạo Alarm.
- Lịch sử thay đổi trạng thái.
- Giá trị Metric tại thời điểm Alarm được kích hoạt.

---

## Các phương pháp khuyến nghị

- Đặt tên Alarm rõ ràng và thống nhất.
- Thiết lập ngưỡng phù hợp với đặc điểm hoạt động của ứng dụng.
- Thường xuyên kiểm tra lịch sử Alarm.
- Chỉ tạo Alarm cho những Metric thực sự quan trọng.
- Tích hợp Alarm với Amazon SNS để tự động gửi thông báo.

---

## Kiểm tra kết quả

Xác nhận rằng:

- Tất cả các Alarm đã được tạo thành công.
- Mỗi Alarm đều ở trạng thái **OK**.
- Có thể xem Metric và lịch sử của từng Alarm.
- Môi trường đã sẵn sàng để tích hợp với Amazon SNS.

---

## Kết quả mong đợi

Sau khi hoàn thành phần này, bạn đã:

- Cấu hình CloudWatch Alarm cho các dịch vụ AWS quan trọng.
- Thiết lập các ngưỡng giám sát hoạt động.
- Biết cách theo dõi trạng thái và lịch sử của Alarm.
- Chuẩn bị hệ thống giám sát để gửi thông báo tự động thông qua Amazon SNS.