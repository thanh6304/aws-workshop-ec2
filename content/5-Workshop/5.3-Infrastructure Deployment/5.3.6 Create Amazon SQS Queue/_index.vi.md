---
title: "5.3.6 Tạo Amazon SQS Queue"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.3.6 Tạo Amazon SQS Queue

## Tổng quan

Amazon Simple Queue Service (Amazon SQS) là dịch vụ hàng đợi thông điệp (Message Queue) được quản lý hoàn toàn bởi AWS.

Trong hệ thống **AI Supply Chain Control Tower**, Amazon SQS đóng vai trò trung gian giữa **Backend API** và **AI Processing Pipeline**.

Thay vì Lambda API xử lý AI ngay sau khi nhận yêu cầu từ người dùng, hệ thống sẽ gửi một thông điệp (Message) vào hàng đợi SQS. Sau đó, Lambda Worker sẽ tự động lấy thông điệp từ hàng đợi để xử lý.

Kiến trúc này giúp:

- Giảm thời gian phản hồi của API.
- Xử lý bất đồng bộ (Asynchronous Processing).
- Tăng khả năng mở rộng.
- Hạn chế mất dữ liệu khi hệ thống gặp lỗi.
- Dễ dàng xử lý lại các tác vụ thất bại.

---

# Kiến trúc

```
User
 │
 ▼
API Gateway
 │
 ▼
Lambda API
 │
 │ Send Message
 ▼
Amazon SQS
 │
 │ Trigger
 ▼
Lambda Worker
 │
 ▼
Athena
 │
 ▼
Amazon Bedrock
 │
 ▼
Amazon RDS
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo Standard Queue.
- Cấu hình Visibility Timeout.
- Cấu hình Message Retention.
- Tạo Dead-Letter Queue (DLQ).
- Kiểm tra Queue hoạt động.

---

# Tại sao sử dụng Amazon SQS?

Nếu Lambda API thực hiện toàn bộ quá trình phân tích AI ngay lập tức, người dùng sẽ phải chờ trong thời gian dài.

Bằng cách sử dụng Amazon SQS:

- API chỉ mất vài trăm mili giây để phản hồi.
- Việc phân tích AI được xử lý ở phía sau.
- Hệ thống vẫn hoạt động ổn định khi có nhiều yêu cầu đồng thời.

Đây là mô hình **Event-Driven Architecture** được AWS khuyến nghị.

---

# Các bước thực hiện

## Bước 1. Mở Amazon SQS

Đăng nhập AWS Management Console.

Tìm kiếm:

```
Amazon SQS
```

Chọn:

```
Queues
```

![Kiến trúc Amazon SQS](/images/5-Workshop/5.3-S3-vpc/5.3.6.png)

---

## Bước 2. Tạo Queue

Chọn:

```
Create Queue
```

---

## Bước 3. Chọn Queue Type

Chọn:

```
Standard Queue
```

Standard Queue phù hợp với các tác vụ AI vì có khả năng mở rộng cao và không yêu cầu thứ tự xử lý nghiêm ngặt.

---

## Bước 4. Cấu hình Queue

| Thuộc tính           | Giá trị             |
| -------------------- | ------------------- |
| Queue Name           | ai-processing-queue |
| Queue Type           | Standard            |
| Visibility Timeout   | 60 Seconds          |
| Message Retention    | 4 Days              |
| Delivery Delay       | 0                   |
| Maximum Message Size | 256 KB              |

> Chèn hình: Queue Configuration

---

## Bước 5. Tạo Dead-Letter Queue

Tạo thêm một Queue:

```
ai-processing-dlq
```

Sau đó quay lại Queue chính và cấu hình:

| Thuộc tính        | Giá trị           |
| ----------------- | ----------------- |
| Dead-Letter Queue | ai-processing-dlq |
| Maximum Receives  | 5                 |

Nếu một thông điệp xử lý thất bại quá 5 lần, AWS sẽ tự động chuyển thông điệp sang DLQ để tránh lặp vô hạn.

---

## Bước 6. Tạo Queue

Nhấn:

```
Create Queue
```

AWS sẽ tạo Queue trong vài giây.

---

## Bước 7. Kiểm tra Queue

Danh sách Queue sẽ hiển thị:

| Queue               |
| ------------------- |
| ai-processing-queue |
| ai-processing-dlq   |

> Chèn hình: Queue List

---

# Best Practices

AWS khuyến nghị:

- Sử dụng Standard Queue cho hệ thống AI và phân tích dữ liệu.
- Thiết lập Dead-Letter Queue để xử lý lỗi.
- Không gửi dữ liệu quá lớn vào Message; chỉ gửi metadata hoặc đường dẫn đến dữ liệu trong Amazon S3.
- Kết hợp SQS với Lambda để xây dựng kiến trúc Serverless Event-Driven.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo thành công Amazon SQS Standard Queue.
- Cấu hình Dead-Letter Queue.
- Hoàn thiện tầng xử lý bất đồng bộ cho hệ thống AI Supply Chain Control Tower.
- Chuẩn bị cho Lambda Worker xử lý dữ liệu trong chương **5.5 AI Pipeline Deployment**.
