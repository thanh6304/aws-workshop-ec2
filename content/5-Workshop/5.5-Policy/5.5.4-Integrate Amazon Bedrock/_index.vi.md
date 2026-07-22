---
title: "5.5.4 Tích hợp Amazon Bedrock"
date: 2024-01-01
weight: 4
chapter: false
---

# 5.5.4 Tích hợp Amazon Bedrock

## Tổng quan

Amazon Bedrock là dịch vụ AI tạo sinh (Generative AI) được quản lý hoàn toàn bởi AWS, cho phép ứng dụng sử dụng nhiều Foundation Models (FMs) từ Amazon và các đối tác mà không cần triển khai hay quản lý hạ tầng Machine Learning.

Trong hệ thống **AI Supply Chain Control Tower**, AI Worker Lambda sẽ sử dụng Amazon Bedrock để phân tích dữ liệu chuỗi cung ứng được truy vấn từ Amazon Athena và tạo ra các báo cáo, dự báo cũng như khuyến nghị hỗ trợ ra quyết định.

---

# Kiến trúc

```
Amazon Athena
        │
        ▼
AI Worker Lambda
        │
        ▼
Amazon Bedrock
        │
        ▼
AI Recommendation
        │
        ▼
Amazon RDS PostgreSQL
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Kích hoạt quyền sử dụng Amazon Bedrock.
- Lựa chọn Foundation Model.
- Cấu hình IAM cho Lambda.
- Gửi Prompt đến Amazon Bedrock.
- Nhận kết quả phân tích AI.

---

# Điều kiện tiên quyết

Đảm bảo:

- AI Worker Lambda đã được triển khai.
- Amazon Athena có thể truy vấn dữ liệu.
- IAM Role của Lambda đã được cấp quyền sử dụng Amazon Bedrock.

---

# Các bước thực hiện

## Bước 1. Mở Amazon Bedrock Console

Đăng nhập AWS Management Console.

Tìm kiếm:

```
Amazon Bedrock
```

Chọn:

```
Amazon Bedrock
```

![Amazon Bedrock function ](/images/5-Workshop/5.5-Policy/5.5.4.png)

---

## Bước 2. Kích hoạt Foundation Model

Trong menu:

```
Model access
```

Nhấn:

```
Manage model access
```

Chọn Foundation Model mong muốn.

Ví dụ:

```
Amazon Nova Lite
```

hoặc

```
Amazon Nova Pro
```

Sau đó chọn:

```
Request access
```

Chờ AWS phê duyệt (nếu cần).

> Chèn hình: Model Access

---

## Bước 3. Cấp quyền cho Lambda

IAM Role của AI Worker cần có quyền:

```json
{
  "Effect": "Allow",
  "Action": ["bedrock:InvokeModel"],
  "Resource": "*"
}
```

Nếu sử dụng workshop, Role:

```
AI-SupplyChain-LambdaRole
```

đã được gán quyền AmazonBedrockFullAccess.

Trong Production, AWS khuyến nghị chỉ cấp quyền cho các Foundation Models cần sử dụng.

---

## Bước 4. Chuẩn bị Prompt

Sau khi truy vấn dữ liệu từ Amazon Athena, Lambda Worker tạo Prompt.

Ví dụ:

```
Inventory

Laptop Dell: 8 units

Mouse Logitech: 12 units

Keyboard: 5 units

Orders Today: 356

Delayed Shipments: 18
```

Prompt:

```
You are a supply chain analyst.

Analyze the inventory and shipment information.

Provide:

- Inventory risks
- Demand forecast
- Restocking recommendations
- Operational suggestions

Return the result in plain text.
```

---

## Bước 5. Gọi Amazon Bedrock

Lambda gửi Prompt đến Foundation Model.

Ví dụ:

```
InvokeModel

↓

Amazon Nova Lite
```

Bedrock sẽ xử lý yêu cầu và sinh kết quả.

---

## Bước 6. Nhận kết quả AI

Ví dụ:

```
Inventory Analysis

• Laptop Dell inventory is critically low.

• Keyboard inventory is below safety stock.

Demand Forecast

Expected demand will increase by approximately 18% next week.

Recommendation

Increase Laptop Dell inventory to at least 40 units.

Review supplier lead times to reduce shipment delays.
```

---

## Bước 7. Chuẩn hóa kết quả

AI Worker sẽ chuyển đổi kết quả AI thành định dạng phù hợp trước khi lưu vào cơ sở dữ liệu.

Ví dụ:

```json
{
  "warehouse": "WH001",
  "generatedAt": "2026-07-22T09:30:00Z",
  "summary": "Inventory is below the safety threshold.",
  "recommendation": "Increase inventory for Laptop Dell and Keyboard."
}
```

---

# Luồng xử lý

```
Amazon Athena
        │
Inventory Data
        │
        ▼
AI Worker Lambda
        │
Prompt
        ▼
Amazon Bedrock
        │
AI Response
        ▼
Amazon RDS PostgreSQL
```

---

# Kiểm tra kết quả

Đảm bảo:

- Lambda có thể gọi Amazon Bedrock.
- Foundation Model phản hồi thành công.
- Kết quả AI được trả về đầy đủ.
- Không có lỗi IAM hoặc quyền truy cập.

---

# Best Practices

AWS khuyến nghị:

- Thiết kế Prompt rõ ràng và có cấu trúc.
- Chỉ gửi dữ liệu cần thiết để giảm chi phí và thời gian xử lý.
- Kiểm tra và xử lý các trường hợp phản hồi lỗi từ Foundation Model.
- Theo dõi số lượng yêu cầu và độ trễ bằng Amazon CloudWatch.
- Áp dụng nguyên tắc Least Privilege cho quyền `bedrock:InvokeModel`.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Kích hoạt và cấu hình Amazon Bedrock.
- Tích hợp AI Worker Lambda với Foundation Model.
- Gửi dữ liệu từ Amazon Athena đến Amazon Bedrock.
- Nhận kết quả phân tích và khuyến nghị bằng AI.
