---
title: "5.8 Kiểm thử hệ thống"
date: 2024-01-01
weight: 8
chapter: false
---

## Tổng quan

Sau khi triển khai toàn bộ hạ tầng, Backend API, AI Pipeline, Data Lake, Monitoring và Security, bước tiếp theo là kiểm thử toàn bộ hệ thống.

Trong chương này, bạn sẽ xác nhận rằng tất cả các thành phần của AI Supply Chain Control Tower hoạt động chính xác từ đầu đến cuối.

Quy trình kiểm thử bao gồm:

- Đăng nhập người dùng.
- Gửi yêu cầu đến Backend API.
- Lưu dữ liệu vào Amazon RDS.
- Gửi tác vụ đến Amazon SQS.
- AI Worker xử lý dữ liệu.
- Truy vấn Amazon Athena.
- Phân tích bằng Amazon Bedrock.
- Hiển thị kết quả trên Dashboard.

---

## Kiến trúc

```text
User
   │
   ▼
Amazon Cognito
   │
JWT
   │
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
Dashboard
```

---

## Mục tiêu

Sau khi hoàn thành chương này, bạn sẽ:

- Kiểm thử toàn bộ hệ thống.
- Xác nhận các dịch vụ AWS giao tiếp đúng.
- Kiểm tra AI Pipeline.
- Kiểm tra Dashboard.
- Phát hiện lỗi nếu có.

---

## Nội dung

Chương này gồm các bài:

- 5.8.1 Test User Authentication
- 5.8.2 Test Backend API
- 5.8.3 Test AI Processing Pipeline
- 5.8.4 Test Analytics Queries
- 5.8.5 Test Dashboard
- 5.8.6 End-to-End Validation

---

## Kết quả mong đợi

Sau khi hoàn thành chương này, toàn bộ AI Supply Chain Control Tower sẽ được kiểm thử thành công trước khi dọn dẹp tài nguyên AWS.
