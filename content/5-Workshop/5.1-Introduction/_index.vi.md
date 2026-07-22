---
title: "Giới thiệu"
date: 2024-01-01
weight: 1
chapter: false
pre: "<b>5.1. </b>"
---

## Tổng quan

Quản lý chuỗi cung ứng đóng vai trò quan trọng trong việc giúp doanh nghiệp theo dõi hàng tồn kho, đơn hàng, nhà cung cấp và hoạt động logistics. Khi quy mô doanh nghiệp ngày càng mở rộng, các phương pháp quản lý truyền thống gặp nhiều hạn chế trong việc xử lý dữ liệu theo thời gian thực và hỗ trợ ra quyết định.

Workshop này giới thiệu **AI Supply Chain Control Tower**, một hệ thống quản lý chuỗi cung ứng thông minh được xây dựng trên nền tảng **Amazon Web Services (AWS)**. Hệ thống kết hợp kiến trúc Serverless, dịch vụ phân tích dữ liệu và Trí tuệ nhân tạo (AI) nhằm cung cấp khả năng giám sát tập trung, dự báo nhu cầu, phân tích rủi ro và hỗ trợ ra quyết định cho doanh nghiệp.

Toàn bộ hệ thống được thiết kế theo các nguyên tắc của **AWS Well-Architected Framework**, hướng đến khả năng mở rộng, tính sẵn sàng cao, bảo mật, tối ưu chi phí và dễ dàng vận hành.

---

## Mục tiêu

Sau khi hoàn thành workshop này, bạn sẽ có thể:

- Triển khai hệ thống AI Supply Chain Control Tower trên nền tảng AWS.
- Xây dựng hệ thống xác thực người dùng bằng Amazon Cognito.
- Xây dựng REST API bằng Amazon API Gateway và AWS Lambda.
- Lưu trữ dữ liệu giao dịch bằng Amazon RDS for PostgreSQL.
- Xây dựng Data Lake bằng Amazon S3.
- Phân tích dữ liệu với AWS Glue và Amazon Athena.
- Tích hợp Amazon Bedrock để dự báo nhu cầu và đưa ra khuyến nghị bằng AI.
- Thiết lập hệ thống giám sát bằng Amazon CloudWatch và Amazon SNS.
- Áp dụng các dịch vụ bảo mật như AWS IAM, AWS KMS, AWS Secrets Manager và AWS CloudTrail.

---

## Kiến trúc hệ thống

Hệ thống AI Supply Chain Control Tower được xây dựng theo kiến trúc Cloud Native gồm các lớp chính:

- Frontend Layer
- Authentication Layer
- API Layer
- Compute Layer
- Data Layer
- AI & Analytics Layer
- Monitoring Layer
- Security & Governance Layer

Frontend được triển khai bằng **AWS Amplify** và giao tiếp với Backend thông qua **Amazon API Gateway**. Người dùng được xác thực bằng **Amazon Cognito**, sau đó các yêu cầu được xử lý bởi **AWS Lambda**.

Dữ liệu giao dịch được lưu trữ trên **Amazon RDS for PostgreSQL**, trong khi dữ liệu phân tích được lưu trên **Amazon S3 Data Lake**. **AWS Glue** và **Amazon Athena** được sử dụng để xử lý và phân tích dữ liệu, còn **Amazon Bedrock** cung cấp các chức năng AI như dự báo nhu cầu và đưa ra khuyến nghị.

> **Sơ đồ kiến trúc hệ thống**

<center>

![AI Supply Chain Architecture](/images/5-Workshop/5.1-Workshop-overview/5.1.png)

</center>

---

## Các dịch vụ AWS sử dụng

| Dịch vụ                   | Mục đích sử dụng                                    |
| ------------------------- | --------------------------------------------------- |
| Amazon Route 53           | Quản lý DNS và tên miền                             |
| AWS WAF                   | Bảo vệ ứng dụng Web khỏi các cuộc tấn công phổ biến |
| AWS Amplify               | Triển khai và lưu trữ giao diện Web                 |
| Amazon Cognito            | Xác thực và quản lý người dùng                      |
| Amazon API Gateway        | Cung cấp REST API                                   |
| AWS Lambda                | Xử lý nghiệp vụ Backend và AI                       |
| Amazon RDS for PostgreSQL | Lưu trữ dữ liệu giao dịch                           |
| Amazon SQS                | Xử lý tác vụ bất đồng bộ                            |
| Amazon S3                 | Lưu trữ Data Lake                                   |
| AWS Glue                  | Quản lý Metadata và ETL                             |
| Amazon Athena             | Phân tích dữ liệu trên Amazon S3                    |
| Amazon Bedrock            | Dự báo và đưa ra khuyến nghị bằng AI                |
| Amazon CloudWatch         | Giám sát, ghi log và thu thập Metrics               |
| Amazon SNS                | Gửi Email cảnh báo                                  |
| AWS IAM                   | Quản lý quyền truy cập                              |
| AWS Secrets Manager       | Quản lý thông tin bí mật                            |
| AWS KMS                   | Mã hóa dữ liệu                                      |
| AWS CloudTrail            | Ghi nhận lịch sử thao tác trên AWS                  |
| AWS CloudFormation        | Triển khai hạ tầng dưới dạng Infrastructure as Code |

---

## Kết quả đạt được

Sau khi hoàn thành workshop này, bạn sẽ triển khai thành công một hệ thống AI Supply Chain Control Tower trên AWS với các chức năng chính:

- Xác thực người dùng an toàn.
- Cung cấp RESTful API.
- Quản lý dữ liệu giao dịch.
- Xây dựng Data Lake phục vụ phân tích.
- Tích hợp AI để dự báo và hỗ trợ ra quyết định.
- Thiết lập hệ thống giám sát, ghi log và cảnh báo.
- Áp dụng các dịch vụ bảo mật theo AWS Best Practices.
