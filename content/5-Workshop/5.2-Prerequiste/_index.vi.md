---
title: "Chuẩn bị"
date: 2024-01-01
weight: 2
chapter: false
pre: "<b>5.2. </b>"
---

## Tổng quan

Trước khi triển khai hệ thống **AI Supply Chain Control Tower**, chúng ta cần chuẩn bị môi trường làm việc, tài khoản AWS và các công cụ cần thiết để đảm bảo quá trình triển khai diễn ra thuận lợi.

Trong workshop này, toàn bộ tài nguyên sẽ được triển khai tại **AWS Region Asia Pacific (Singapore)** nhằm đảm bảo tính ổn định, chi phí hợp lý và khả năng tương thích với các dịch vụ AI.

---

## Mục tiêu

Sau khi hoàn thành phần này, bạn sẽ:

- Có tài khoản AWS sẵn sàng sử dụng.
- Chuẩn bị môi trường phát triển.
- Cấu hình AWS CLI.
- Kết nối GitHub Repository.
- Lựa chọn AWS Region phù hợp.
- Kiểm tra các quyền cần thiết trước khi triển khai.

---

## Yêu cầu

Để thực hiện workshop, cần chuẩn bị các thành phần sau:

### AWS Account

- Đã đăng ký tài khoản AWS.
- Có quyền tạo các dịch vụ cần thiết.
- Khuyến nghị sử dụng IAM User thay vì Root Account.

---

### AWS Region

Workshop sử dụng Region:

```
Asia Pacific (Singapore)

ap-southeast-1
```

---

### Công cụ cần cài đặt

- Visual Studio Code
- Git
- Node.js (LTS)
- AWS CLI Version 2
- PostgreSQL Client (hoặc pgAdmin)
- Postman

---

### Source Code

Chuẩn bị source code của dự án:

- Frontend (ReactJS)
- Backend (Node.js / Python)
- AWS Lambda Functions

Đưa source code lên GitHub để phục vụ triển khai AWS Amplify.

---

### Kiểm tra AWS CLI

Sau khi cài đặt AWS CLI, kiểm tra phiên bản:

```bash
aws --version
```

Ví dụ:

```bash
aws-cli/2.17.xx
```

---

### Cấu hình AWS CLI

Thực hiện cấu hình:

```bash
aws configure
```

Nhập các thông tin:

```text
AWS Access Key ID
AWS Secret Access Key
Region: ap-southeast-1
Output format: json
```

---

### Kiểm tra kết nối

Kiểm tra thông tin tài khoản:

```bash
aws sts get-caller-identity
```

Nếu cấu hình thành công, AWS sẽ trả về:

- Account ID
- User ARN
- User ID

---

## Các dịch vụ AWS sẽ sử dụng

Trong workshop này, chúng ta sẽ triển khai các dịch vụ sau:

- AWS Amplify
- Amazon Cognito
- Amazon API Gateway
- AWS Lambda
- Amazon RDS for PostgreSQL
- Amazon S3
- AWS Glue
- Amazon Athena
- Amazon Bedrock
- Amazon SQS
- Amazon CloudWatch
- Amazon SNS
- AWS IAM
- AWS Secrets Manager
- AWS KMS
- AWS CloudTrail
- AWS CloudFormation

---

## Kết quả mong đợi

Sau khi hoàn thành bước chuẩn bị, bạn sẽ có:

- Tài khoản AWS sẵn sàng sử dụng.
- AWS CLI đã được cấu hình.
- Source code trên GitHub.
- Môi trường phát triển hoàn chỉnh.
- Sẵn sàng triển khai hạ tầng AWS ở các bước tiếp theo.
  ![Frontend & Authentication](/images/5-Workshop/5.2-Workshop-overview/5.2.png)
