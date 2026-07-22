---
title: "Workshop"
date: 2026-06-07
weight: 5
chapter: false
pre: " <b> 5. </b> "
---


# Triển khai AI Supply Chain Control Tower trên AWS

#### Tổng quan

**AI Supply Chain Control Tower** là hệ thống hỗ trợ doanh nghiệp quản lý và giám sát chuỗi cung ứng bằng cách kết hợp các dịch vụ AWS Cloud và trí tuệ nhân tạo (AI). Hệ thống cho phép quản lý dữ liệu tập trung, xử lý nghiệp vụ theo mô hình serverless, phân tích dữ liệu trên Data Lake và đưa ra các dự báo, đánh giá rủi ro cũng như khuyến nghị thông minh.

Trong workshop này, chúng ta sẽ từng bước xây dựng và triển khai hệ thống trên AWS, bao gồm việc cấu hình hạ tầng, triển khai Backend API, xây dựng Data Lake, tích hợp AI với Amazon Bedrock và thiết lập các dịch vụ giám sát, bảo mật.

Kiến trúc của hệ thống sử dụng các dịch vụ chính như:

- **Amazon Route 53** – Quản lý DNS và điều hướng tên miền.
- **AWS Amplify** – Triển khai giao diện Web.
- **Amazon Cognito** – Xác thực và quản lý người dùng.
- **Amazon API Gateway** – Tiếp nhận và định tuyến API Request.
- **AWS Lambda** – Xử lý nghiệp vụ và AI.
- **Amazon RDS PostgreSQL** – Lưu trữ dữ liệu giao dịch.
- **Amazon SQS** – Xử lý tác vụ bất đồng bộ.
- **Amazon S3** – Lưu trữ Data Lake.
- **AWS Glue & Amazon Athena** – ETL và phân tích dữ liệu.
- **Amazon Bedrock** – Cung cấp mô hình AI phục vụ dự báo và khuyến nghị.
- **Amazon CloudWatch & Amazon SNS** – Giám sát và gửi cảnh báo.
- **AWS IAM, KMS, Secrets Manager và CloudTrail** – Đảm bảo bảo mật và quản trị hệ thống.

#### Nội dung

1. [Giới thiệu về AI Supply Chain Control Tower](5.1-Introduction/)
2. [Chuẩn bị môi trường AWS](5.2-Prerequisite/)
3. [Triển khai hạ tầng AWS](5.3-Infrastructure/)
4. [Triển khai Backend API](5.4-Backend/)
5. [Triển khai AI Pipeline](5.5-AI-Pipeline/)
6. [Triển khai Data Lake & Analytics](5.6-Data-Lake/)
7. [Giám sát và bảo mật hệ thống](5.7-Monitoring-Security/)
8. [Kiểm thử và đánh giá hệ thống](5.8-Testing/)
9. [Dọn dẹp tài nguyên](5.9-Cleanup/)
