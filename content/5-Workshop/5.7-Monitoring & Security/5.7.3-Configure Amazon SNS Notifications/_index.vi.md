---
title: "5.7.3 Cấu hình thông báo với Amazon SNS"
date: 2024-01-01
weight: 3
chapter: false
---


## Tổng quan

Trong phần trước, bạn đã tạo các CloudWatch Alarm để giám sát hệ thống. Tuy nhiên, nếu không có cơ chế thông báo, quản trị viên sẽ phải chủ động đăng nhập vào AWS Console để kiểm tra trạng thái của các Alarm.

Amazon Simple Notification Service (Amazon SNS) giúp gửi thông báo tự động khi Alarm chuyển trạng thái.

Trong workshop này, Amazon SNS sẽ gửi email cảnh báo cho quản trị viên khi:

- Lambda phát sinh lỗi.
- API Gateway trả về lỗi 5XX.
- CPU của Amazon RDS vượt ngưỡng.
- Amazon SQS có quá nhiều tin nhắn chờ xử lý.
- Amazon Athena có truy vấn thất bại.

---

## Kiến trúc

```text
AWS Services
      │
      ▼
CloudWatch Metrics
      │
      ▼
CloudWatch Alarms
      │
      ▼
Amazon SNS Topic
      │
      ▼
Email Notification
      │
      ▼
Administrator
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Tạo SNS Topic.
- Đăng ký địa chỉ email nhận thông báo.
- Liên kết CloudWatch Alarm với SNS Topic.
- Kiểm tra việc gửi và nhận thông báo.

---

# Bước 1. Mở Amazon SNS

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
Amazon SNS
```

Chọn:

```text
Amazon Simple Notification Service
```

---

# Bước 2. Tạo SNS Topic

Chọn:

```text
Topics
```

Nhấn:

```text
Create topic
```

Chọn:

```text
Standard
```

Tên Topic:

```text
SupplyChainAlerts
```

Mô tả:

```text
Notification topic for AI Supply Chain Control Tower
```

Nhấn:

```text
Create topic
```

---

# Bước 3. Tạo Subscription

Trong Topic vừa tạo, chọn:

```text
Create subscription
```

Thiết lập:

| Thuộc tính | Giá trị                |
| ---------- | ---------------------- |
| Protocol   | Email                  |
| Endpoint   | your-email@example.com |

Nhấn:

```text
Create subscription
```

---

# Bước 4. Xác nhận Email

Amazon SNS sẽ gửi email xác nhận đến địa chỉ đã đăng ký.

Mở email và chọn:

```text
Confirm subscription
```

Sau khi xác nhận, trạng thái Subscription sẽ là:

```text
Confirmed
```

---

# Bước 5. Liên kết Alarm với SNS Topic

Mở:

```text
CloudWatch

Alarms
```

Chọn Alarm:

```text
BackendLambdaErrors
```

Chọn:

```text
Actions

Edit
```

Trong phần:

```text
Notification
```

Chọn:

```text
SupplyChainAlerts
```

Lặp lại thao tác này cho:

```text
BackendLambdaDuration

ApiGateway5XXErrors

RDSHighCPU

SQSQueueDepth

AthenaFailedQueries
```

---

# Bước 6. Kiểm tra Alarm Action

Mỗi Alarm sẽ có:

```text
State

OK

ALARM

Insufficient Data
```

Khi Alarm chuyển sang:

```text
ALARM
```

CloudWatch sẽ gửi thông báo đến:

```text
Amazon SNS
```

Amazon SNS sẽ gửi email cho người quản trị.

---

# Bước 7. Kiểm tra Email

Ví dụ email nhận được:

```text
Subject

ALARM:
BackendLambdaErrors

Message

Alarm Name:
BackendLambdaErrors

State:
ALARM

Reason:

Threshold Crossed

Metric:

Errors
```

---

# Bước 8. Kiểm tra SNS Metrics

Trong Amazon SNS, chọn:

```text
Metrics
```

Theo dõi:

```text
NumberOfMessagesPublished

NumberOfNotificationsDelivered

NumberOfNotificationsFailed
```

Các chỉ số này giúp xác nhận thông báo đã được gửi thành công.

---

## Best Practices

- Sử dụng một SNS Topic cho các cảnh báo cùng mục đích.
- Xác nhận Subscription ngay sau khi tạo.
- Đặt tên Topic rõ ràng.
- Chỉ đăng ký các địa chỉ email cần thiết.
- Kết hợp CloudWatch Alarm với SNS để tự động hóa quy trình giám sát.

---

## Kiểm tra kết quả

Đảm bảo:

- SNS Topic `SupplyChainAlerts` được tạo.
- Subscription ở trạng thái **Confirmed**.
- Các Alarm được liên kết với SNS Topic.
- Email được gửi khi Alarm chuyển sang trạng thái **ALARM**.
- SNS Metrics ghi nhận việc gửi thông báo.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo Amazon SNS Topic.
- Đăng ký và xác nhận email nhận thông báo.
- Liên kết CloudWatch Alarm với Amazon SNS.
- Thiết lập hệ thống gửi cảnh báo tự động cho AI Supply Chain Control Tower.
