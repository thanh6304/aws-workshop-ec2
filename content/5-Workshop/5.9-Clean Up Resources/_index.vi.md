---
title: "5.9 Dọn dẹp tài nguyên"
date: 2024-01-01
weight: 9
chapter: false
---

## Tổng quan

Sau khi hoàn thành workshop, bạn nên xóa các tài nguyên AWS đã tạo để tránh phát sinh chi phí không cần thiết.

Trong bài này, bạn sẽ xóa toàn bộ tài nguyên theo đúng thứ tự nhằm đảm bảo không còn tài nguyên phụ thuộc và quá trình dọn dẹp diễn ra thuận lợi.

---

## Kiến trúc

```text
Frontend
   │
AWS Amplify
   │
Amazon Cognito
   │
API Gateway
   │
Lambda Functions
   │
Amazon SQS
   │
Amazon RDS
   │
Amazon S3
   │
AWS Glue
   │
Amazon Athena
   │
CloudWatch
   │
SNS
   │
CloudTrail
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Xóa toàn bộ tài nguyên AWS đã triển khai.
- Đảm bảo không còn phát sinh chi phí.
- Kiểm tra tài khoản AWS đã được dọn dẹp hoàn toàn.

---

# Bước 1. Xóa ứng dụng AWS Amplify

Mở:

```text
AWS Amplify
```

Chọn ứng dụng:

```text
AI Supply Chain Control Tower
```

Chọn:

```text
Actions

↓

Delete App
```

Xác nhận thao tác xóa.

---

# Bước 2. Xóa Amazon Cognito

Mở:

```text
Amazon Cognito

↓

User Pools
```

Chọn User Pool:

```text
SupplyChainUserPool
```

Chọn:

```text
Delete
```

Xác nhận tên User Pool để hoàn tất.

---

# Bước 3. Xóa API Gateway

Mở:

```text
Amazon API Gateway
```

Chọn HTTP API.

Chọn:

```text
Delete API
```

Xác nhận thao tác.

---

# Bước 4. Xóa Lambda Functions

Mở:

```text
AWS Lambda
```

Xóa lần lượt:

```text
backend-api

ai-worker
```

Đảm bảo không còn Lambda Function nào của workshop.

---

# Bước 5. Xóa Amazon SQS

Mở:

```text
Amazon SQS
```

Chọn Queue:

```text
SupplyChainQueue
```

Chọn:

```text
Delete
```

---

# Bước 6. Xóa Amazon RDS

Mở:

```text
Amazon RDS

↓

Databases
```

Chọn:

```text
supplychain-db
```

Chọn:

```text
Delete
```

Nếu không cần sao lưu cuối cùng, bỏ chọn:

```text
Create Final Snapshot
```

Xác nhận thao tác xóa.

---

# Bước 7. Xóa Amazon S3

Mở:

```text
Amazon S3
```

Chọn Bucket:

```text
ai-supplychain-datalake
```

Trước tiên:

```text
Empty Bucket
```

Sau đó:

```text
Delete Bucket
```

Đảm bảo Bucket đã được xóa hoàn toàn.

---

# Bước 8. Xóa AWS Glue và Amazon Athena

Mở:

```text
AWS Glue
```

Xóa:

- Database
- Tables
- Crawlers

Sau đó mở:

```text
Amazon Athena
```

Nếu không còn sử dụng, xóa hoặc dọn thư mục kết quả truy vấn trong Amazon S3.

---

# Bước 9. Xóa Secrets Manager

Mở:

```text
AWS Secrets Manager
```

Chọn:

```text
SupplyChainDatabaseSecret
```

Chọn:

```text
Delete Secret
```

---

# Bước 10. Xóa SNS

Mở:

```text
Amazon SNS
```

Xóa Topic:

```text
SupplyChainAlerts
```

---

# Bước 11. Xóa CloudTrail

Mở:

```text
AWS CloudTrail
```

Xóa:

```text
SupplyChainAuditTrail
```

Nếu đã tạo Bucket riêng cho CloudTrail, hãy xóa Bucket sau khi xóa Trail.

---

# Bước 12. Xóa CloudWatch Resources

Mở:

```text
Amazon CloudWatch
```

Xóa:

- Dashboard
- Alarms
- Log Groups (nếu không còn cần lưu)

Ví dụ:

```text
/aws/lambda/backend-api

/aws/lambda/ai-worker
```

---

# Bước 13. Xóa IAM Roles

Mở:

```text
AWS IAM
```

Xóa các Role được tạo riêng cho workshop, ví dụ:

```text
BackendLambdaRole

AIWorkerLambdaRole
```

Chỉ xóa khi chắc chắn các Role này không còn được sử dụng.

---

# Bước 14. Kiểm tra AWS Billing

Mở:

```text
AWS Billing

↓

Bills
```

Kiểm tra:

- Không còn tài nguyên đang hoạt động.
- Không phát sinh chi phí ngoài mong muốn.

Đây là bước quan trọng để đảm bảo môi trường đã được dọn dẹp hoàn toàn.

---

## Best Practices

- Xóa tài nguyên ngay sau khi hoàn thành workshop.
- Kiểm tra các Bucket S3 đã được làm trống trước khi xóa.
- Chỉ xóa IAM Roles và KMS Keys nếu chắc chắn không còn được sử dụng.
- Theo dõi AWS Billing trong vài ngày sau khi dọn dẹp để phát hiện tài nguyên còn sót lại.

---

## Kiểm tra kết quả

Đảm bảo:

- AWS Amplify đã được xóa.
- Amazon Cognito đã được xóa.
- API Gateway không còn tồn tại.
- Lambda Functions đã được xóa.
- Amazon RDS đã được xóa.
- Amazon S3 Bucket đã được xóa.
- AWS Glue và Athena đã được dọn dẹp.
- SNS, CloudTrail và CloudWatch đã được xóa.
- Không còn tài nguyên của workshop trong tài khoản AWS.

---

## Kết quả

Chúc mừng!

Bạn đã hoàn thành workshop **AI Supply Chain Control Tower on AWS**.

Trong workshop này, bạn đã:

- Xây dựng kiến trúc serverless trên AWS.
- Triển khai Backend API bằng AWS Lambda.
- Xây dựng Data Lake với Amazon S3 và AWS Glue.
- Phân tích dữ liệu bằng Amazon Athena.
- Tích hợp Generative AI với Amazon Bedrock.
- Giám sát và bảo mật hệ thống bằng CloudWatch, IAM, Secrets Manager, KMS và CloudTrail.
- Kiểm thử toàn bộ hệ thống từ đầu đến cuối.
- Dọn dẹp toàn bộ tài nguyên để tối ưu chi phí AWS.

Workshop đã hoàn tất và môi trường AWS đã sẵn sàng cho các dự án hoặc thử nghiệm tiếp theo.
