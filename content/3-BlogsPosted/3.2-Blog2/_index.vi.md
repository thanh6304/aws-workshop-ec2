---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# AWS Secrets Manager, Parameter Store hay AppConfig?

### Chọn đúng dịch vụ để quản lý Secrets và Configuration trên AWS

Trong quá trình tìm hiểu AWS Security, mình nhận thấy nhiều người thường nhầm lẫn giữa **AWS Secrets Manager**, **AWS Systems Manager Parameter Store** và **AWS AppConfig**. Mặc dù cả ba đều liên quan đến việc quản lý dữ liệu cấu hình, nhưng mỗi dịch vụ lại được thiết kế cho một mục đích khác nhau.

## Tổng quan

Mặc dù **AWS Secrets Manager**, **AWS Systems Manager Parameter Store** và **AWS AppConfig** đều được sử dụng để quản lý thông tin của ứng dụng, nhưng mỗi dịch vụ được thiết kế cho một mục đích khác nhau.

- **Secrets Manager**: Quản lý các thông tin nhạy cảm như mật khẩu, API Key và Access Token.
- **Parameter Store**: Quản lý các tham số và cấu hình của ứng dụng như Environment Variables, Endpoint hoặc Region.
- **AppConfig**: Quản lý và triển khai cấu hình động, Feature Flags và các thay đổi cấu hình mà không cần triển khai lại ứng dụng.

Việc lựa chọn đúng dịch vụ không chỉ giúp tăng tính bảo mật mà còn giúp hệ thống dễ quản lý, dễ mở rộng và tối ưu chi phí vận hành.

---

## AWS Secrets Manager

Secrets Manager được sử dụng để lưu trữ các thông tin nhạy cảm như:

- Database Password
- API Key
- OAuth Token
- JWT Secret
- Access Token

Điểm nổi bật của dịch vụ này:

- Tự động xoay (Automatic Rotation).
- Mã hóa bằng AWS KMS.
- Hỗ trợ Multi-Region Replication.
- Tích hợp với nhiều dịch vụ AWS.

Đây là lựa chọn phù hợp khi ứng dụng cần quản lý các thông tin xác thực có yêu cầu bảo mật cao.

---

## AWS Systems Manager Parameter Store

Parameter Store phù hợp để lưu các cấu hình thông thường của ứng dụng.

Ví dụ:

- API Endpoint
- AWS Region
- S3 Bucket Name
- Environment Variables
- AMI ID
- License Key

Ưu điểm:

- Chi phí thấp.
- Hỗ trợ SecureString với AWS KMS.
- Dễ tích hợp với EC2, Lambda và các dịch vụ AWS khác.

Nếu cấu hình ít thay đổi và không cần Rotation thì Parameter Store là lựa chọn hợp lý.

---

## AWS AppConfig

Khác với hai dịch vụ trên, AppConfig giúp triển khai cấu hình ứng dụng một cách an toàn.

Một số trường hợp sử dụng:

- Feature Flags
- Thay đổi tỷ lệ Rollout
- Cập nhật cấu hình AI Model
- Điều chỉnh Allowlist

Điểm nổi bật:

- Validate cấu hình trước khi triển khai.
- Rollout theo từng giai đoạn.
- Tự động Rollback khi phát hiện lỗi thông qua CloudWatch.

AppConfig rất phù hợp với các hệ thống DevOps và CI/CD hiện đại.

---

## Nên chọn dịch vụ nào?

### Chọn Secrets Manager khi

- Lưu Password.
- Lưu API Key.
- Lưu Access Token.
- Cần Automatic Rotation.
- Cần Multi-Region Replication.

### Chọn Parameter Store khi

- Quản lý Configuration.
- Lưu Environment Variables.
- Muốn tối ưu chi phí.
- Không cần Rotation.

### Chọn AppConfig khi

- Quản lý Feature Flags.
- Thay đổi cấu hình động.
- Triển khai cấu hình theo từng giai đoạn.
- Cần Rollback khi có sự cố.

---

## Điều mình rút ra

Sau khi tìm hiểu, mình nhận thấy AWS không xây dựng một dịch vụ làm tất cả mọi việc.

Mỗi dịch vụ đều có vai trò riêng:

- Secrets Manager → Security.
- Parameter Store → Configuration Management.
- AppConfig → Deployment & Feature Management.

Trong một hệ thống thực tế, ba dịch vụ này hoàn toàn có thể được sử dụng kết hợp để vừa đảm bảo bảo mật, vừa dễ quản lý và tối ưu chi phí.

---

## Kết luận

Việc lựa chọn đúng dịch vụ không chỉ giúp hệ thống an toàn hơn mà còn giảm chi phí và đơn giản hóa quá trình vận hành.

Theo mình:

- Secrets Manager phù hợp với dữ liệu nhạy cảm.
- Parameter Store phù hợp với cấu hình thông thường.
- AppConfig phù hợp với việc triển khai và quản lý cấu hình động.

Hiểu rõ vai trò của từng dịch vụ sẽ giúp thiết kế hệ thống AWS hiệu quả hơn.

---

## Nguồn tham khảo

[#AWS](https://www.facebook.com/hashtag/aws)
[#AWSSecurity](https://www.facebook.com/hashtag/awssecurity)
[#SecretsManager](https://www.facebook.com/hashtag/secretsmanager)
[#ParameterStore](https://www.facebook.com/hashtag/parameterstore)
[#AWSAppConfig](https://www.facebook.com/hashtag/awsappconfig)
[#CloudComputing](https://www.facebook.com/hashtag/cloudcomputing)
[#DevOps](https://www.facebook.com/hashtag/devops)
[#AmazonWebServices](https://www.facebook.com/hashtag/amazonwebservices)
[#LearningAWS](https://www.facebook.com/hashtag/learningaws)