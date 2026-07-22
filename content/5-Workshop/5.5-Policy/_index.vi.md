---
title: "Triển khai AI Pipeline"
date: 2024-01-01
weight: 5
chapter: false
pre: "<b>5.5. </b>"
---

## Tổng quan

Sau khi hoàn thành Backend API, bước tiếp theo là xây dựng AI Pipeline để tự động phân tích dữ liệu và hỗ trợ ra quyết định trong chuỗi cung ứng.

Trong chương này, hệ thống sẽ sử dụng các dịch vụ AI và phân tích dữ liệu của AWS để xử lý yêu cầu dự báo được gửi từ Backend.

Quy trình hoạt động gồm:

- Lambda Backend gửi yêu cầu vào Amazon SQS.
- Lambda Worker nhận thông điệp từ hàng đợi.
- Amazon Athena truy vấn dữ liệu từ Amazon S3 Data Lake.
- Amazon Bedrock phân tích dữ liệu và sinh kết quả AI.
- Lambda Worker lưu kết quả về Amazon RDS PostgreSQL.

Nhờ kiến trúc xử lý bất đồng bộ này, Backend phản hồi nhanh hơn, trong khi các tác vụ AI vẫn được thực hiện một cách hiệu quả và có khả năng mở rộng.

---

## Mục tiêu

Sau chương này bạn sẽ:

- Tạo AWS Glue Data Catalog.
- Truy vấn dữ liệu bằng Amazon Athena.
- Triển khai Lambda Worker.
- Tích hợp Amazon Bedrock.
- Xử lý thông điệp từ Amazon SQS.
- Lưu kết quả AI vào PostgreSQL.
- Kiểm thử toàn bộ AI Pipeline.

---

## Kiến trúc

```
Amazon SQS
      │
      ▼
Lambda Worker
      │
      ▼
Amazon Athena
      │
      ▼
Amazon Bedrock
      │
      ▼
Amazon RDS PostgreSQL
```

---

## Nội dung

Trong chương này chúng ta sẽ thực hiện:

- 5.5.1 Create AWS Glue Data Catalog
- 5.5.2 Query Data with Amazon Athena
- 5.5.3 Create AI Worker Lambda
- 5.5.4 Integrate Amazon Bedrock
- 5.5.5 Process Messages from Amazon SQS
- 5.5.6 Store AI Analysis Results
- 5.5.7 Test the AI Pipeline

---

## Kết quả

Sau khi hoàn thành chương này, hệ thống có thể tự động nhận yêu cầu AI, truy vấn dữ liệu, sử dụng Amazon Bedrock để phân tích và lưu kết quả trở lại cơ sở dữ liệu mà không cần người dùng can thiệp.
