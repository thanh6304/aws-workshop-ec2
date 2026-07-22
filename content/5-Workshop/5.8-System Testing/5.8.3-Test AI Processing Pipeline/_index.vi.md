---
title: "5.8.3 Kiểm thử AI Processing Pipeline"
date: 2024-01-01
weight: 3
chapter: false
---



## Tổng quan

Sau khi Backend API lưu dữ liệu vào Amazon RDS, hệ thống sẽ gửi một thông điệp đến Amazon SQS để kích hoạt quá trình xử lý AI.

Trong bài này, bạn sẽ xác minh toàn bộ quy trình từ khi thông điệp được gửi vào hàng đợi đến khi AI hoàn thành phân tích và lưu kết quả.

---

## Kiến trúc

```text
Backend API
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
Amazon RDS
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Kiểm tra Amazon SQS.
- Kiểm tra AI Worker Lambda.
- Kiểm tra Amazon Athena.
- Kiểm tra Amazon Bedrock.
- Kiểm tra kết quả AI được lưu vào cơ sở dữ liệu.

---

# Bước 1. Tạo dữ liệu kiểm thử

Tạo một đơn hàng mới thông qua Backend API.

Ví dụ:

```http
POST /api/orders
```

```json
{
  "customerName": "John Smith",
  "productId": 101,
  "quantity": 10
}
```

Backend sẽ:

```text
Save Order

↓

Amazon RDS

↓

Publish Message

↓

Amazon SQS
```

---

# Bước 2. Kiểm tra Amazon SQS

Mở:

```text
Amazon SQS
```

Chọn Queue:

```text
SupplyChainQueue
```

Kiểm tra:

```text
ApproximateNumberOfMessagesVisible
```

Sau khi AI Worker xử lý thành công, số lượng tin nhắn chờ xử lý sẽ giảm về:

```text
0
```

---

# Bước 3. Kiểm tra AI Worker Lambda

Mở:

```text
AWS Lambda

ai-worker
```

Chọn:

```text
Monitor

View CloudWatch Logs
```

Xác nhận Lambda được kích hoạt bởi thông điệp từ Amazon SQS và không có lỗi trong quá trình xử lý.

---

# Bước 4. Kiểm tra Amazon Athena

AI Worker sẽ thực hiện các truy vấn trên dữ liệu đã được lưu trong Data Lake.

Mở:

```text
Amazon Athena
```

Chọn:

```text
Query History
```

Đảm bảo truy vấn mới đã được thực thi thành công và không có trạng thái **FAILED**.

---

# Bước 5. Kiểm tra Amazon Bedrock

AI Worker gửi dữ liệu phân tích đến Amazon Bedrock.

Ví dụ Prompt:

```text
Analyze current inventory and identify products with a high risk of stock shortages.
```

Đảm bảo AI trả về kết quả phân tích và không phát sinh lỗi khi gọi mô hình.

---

# Bước 6. Kiểm tra kết quả trong Amazon RDS

Sau khi AI hoàn tất xử lý, kết quả được lưu vào bảng phân tích.

Ví dụ:

```sql
SELECT *
FROM ai_insights
ORDER BY created_at DESC;
```

Đảm bảo xuất hiện bản ghi mới với nội dung phân tích.

---

# Bước 7. Kiểm tra CloudWatch Logs

Mở:

```text
CloudWatch

Log Groups

/aws/lambda/ai-worker
```

Xác nhận:

- Lambda được kích hoạt.
- Athena Query thành công.
- Bedrock trả về phản hồi.
- Không có lỗi Runtime.

---

# Bước 8. Kiểm tra xử lý lỗi

Thử mô phỏng một số tình huống:

- Tin nhắn không hợp lệ.
- Thiếu dữ liệu đầu vào.
- Lambda không có quyền truy cập Athena hoặc Bedrock.

Quan sát:

- CloudWatch Logs.
- CloudWatch Metrics.
- CloudWatch Alarms.

Đảm bảo lỗi được ghi nhận đầy đủ và không làm gián đoạn các yêu cầu hợp lệ.

---

## Best Practices

- Thiết kế AI Worker theo hướng idempotent để tránh xử lý trùng lặp.
- Sử dụng Dead-Letter Queue (DLQ) cho các thông điệp xử lý thất bại.
- Giới hạn thời gian thực thi của Lambda phù hợp với khối lượng công việc.
- Ghi log đầy đủ cho từng bước xử lý để hỗ trợ điều tra sự cố.

---

## Kiểm tra kết quả

Đảm bảo:

- Backend gửi thông điệp vào Amazon SQS.
- AI Worker Lambda xử lý thông điệp.
- Amazon Athena thực hiện truy vấn thành công.
- Amazon Bedrock tạo kết quả phân tích.
- Kết quả AI được lưu vào Amazon RDS.
- CloudWatch Logs không ghi nhận lỗi nghiêm trọng.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Xác nhận AI Processing Pipeline hoạt động đúng.
- Kiểm tra quá trình xử lý bất đồng bộ bằng Amazon SQS.
- Xác minh tích hợp giữa Lambda, Athena và Amazon Bedrock.
- Đảm bảo kết quả AI được lưu thành công vào hệ thống.
