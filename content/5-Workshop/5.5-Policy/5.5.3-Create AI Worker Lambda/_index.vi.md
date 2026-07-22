---
title: "5.5.3 Triển khai AI Worker Lambda"
date: 2024-01-01
weight: 3
chapter: false
---

# 5.5.3 Triển khai AI Worker Lambda

## Tổng quan

Trong kiến trúc AI Supply Chain Control Tower, AI Worker Lambda là thành phần chịu trách nhiệm xử lý các yêu cầu phân tích dữ liệu được gửi từ Amazon SQS.

Thay vì xử lý trực tiếp trong Backend API, các tác vụ AI sẽ được thực hiện bất đồng bộ (Asynchronous Processing). Điều này giúp giảm thời gian phản hồi của API và tăng khả năng mở rộng của hệ thống.

Khi nhận được một thông điệp từ Amazon SQS, AI Worker Lambda sẽ:

- Đọc thông tin yêu cầu từ hàng đợi.
- Truy vấn dữ liệu bằng Amazon Athena.
- Chuẩn bị Prompt cho Amazon Bedrock.
- Nhận kết quả phân tích AI.
- Lưu kết quả vào Amazon RDS PostgreSQL.

---

# Kiến trúc

```
Amazon SQS
      │
      ▼
AI Worker Lambda
      │
 ┌────┼──────────────┐
 ▼    ▼              ▼
Athena Bedrock   Amazon RDS
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo AI Worker Lambda.
- Cấu hình Trigger từ Amazon SQS.
- Thiết lập biến môi trường.
- Kết nối Lambda với Athena.
- Chuẩn bị tích hợp Amazon Bedrock.

---

# Các bước thực hiện

## Bước 1. Mở AWS Lambda Console

Đăng nhập AWS Management Console.

Tìm kiếm:

```
AWS Lambda
```

Chọn:

```
Create Function
```

---

## Bước 2. Tạo Lambda Function

Cấu hình:

| Thuộc tính     | Giá trị                   |
| -------------- | ------------------------- |
| Function Name  | ai-supplychain-worker     |
| Runtime        | Java 21                   |
| Architecture   | x86_64                    |
| Execution Role | AI-SupplyChain-LambdaRole |

Nhấn:

```
Create Function
```

![Lambda function ](/images/5-Workshop/5.5-Policy/5.5.3.png)

---

## Bước 3. Cấu hình VPC

Trong mục:

```
Configuration

↓

VPC
```

Chọn:

- Private Subnet A
- Private Subnet B
- Lambda Security Group

Điều này cho phép Lambda truy cập Amazon RDS PostgreSQL một cách an toàn.

---

## Bước 4. Thêm Trigger từ Amazon SQS

Trong Lambda Console:

```
Add Trigger
```

Chọn:

```
Amazon SQS
```

Queue:

```
ai-processing-queue
```

Batch Size:

```
10
```

Enabled:

```
Yes
```

Khi có thông điệp mới trong Queue, Lambda sẽ tự động được kích hoạt.

---

## Bước 5. Thiết lập Environment Variables

Trong mục:

```
Configuration

↓

Environment Variables
```

Thêm:

| Key             | Value                                        |
| --------------- | -------------------------------------------- |
| ATHENA_DATABASE | supplychain_catalog                          |
| ATHENA_OUTPUT   | s3://ai-supplychain-datalake/athena-results/ |
| SECRET_NAME     | ai-supplychain-db-secret                     |
| AWS_REGION      | ap-southeast-1                               |

Các biến này sẽ được Lambda sử dụng trong quá trình truy vấn dữ liệu và lưu kết quả.

---

## Bước 6. Cấu hình Memory và Timeout

Khuyến nghị:

| Thuộc tính | Giá trị    |
| ---------- | ---------- |
| Memory     | 1024 MB    |
| Timeout    | 60 seconds |

Do AI Worker phải truy vấn Athena và gọi Amazon Bedrock, thời gian xử lý có thể dài hơn Backend Lambda.

---

## Bước 7. Triển khai mã nguồn

Đóng gói ứng dụng Spring Boot thành tệp ZIP hoặc JAR tương thích với AWS Lambda.

Triển khai thông qua:

```
Upload from

↓

.zip file
```

Sau khi tải lên, cấu hình Handler phù hợp với ứng dụng Java.

---

## Kiểm tra Trigger

Mở:

```
Configuration

↓

Triggers
```

Đảm bảo Trigger hiển thị:

```
Amazon SQS

↓

ai-processing-queue
```

Điều này xác nhận Lambda sẽ tự động xử lý các thông điệp mới.

---

# Luồng xử lý

```
POST /ai/predict
        │
        ▼
Backend Lambda
        │
        ▼
Amazon SQS
        │
        ▼
AI Worker Lambda
```

---

# Kiểm tra kết quả

Đảm bảo:

- Lambda được tạo thành công.
- Trigger Amazon SQS hoạt động.
- Environment Variables được cấu hình đầy đủ.
- Lambda ở trạng thái **Active**.
- Không có lỗi khi triển khai.

---

# Best Practices

AWS khuyến nghị:

- Chỉ xử lý một nhóm thông điệp phù hợp với Batch Size để tránh quá tải.
- Thiết lập Dead Letter Queue (DLQ) cho Amazon SQS để lưu các thông điệp xử lý thất bại.
- Theo dõi thời gian thực thi và lỗi bằng Amazon CloudWatch.
- Cấu hình Memory và Timeout phù hợp với khối lượng công việc AI.
- Áp dụng nguyên tắc Least Privilege cho IAM Role của Lambda.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Triển khai thành công AI Worker Lambda.
- Kết nối Lambda với Amazon SQS.
- Chuẩn bị môi trường để truy vấn Amazon Athena.
- Sẵn sàng tích hợp Amazon Bedrock trong bước tiếp theo.
