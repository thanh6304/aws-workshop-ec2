---
title: "Triển khai hạ tầng AWS"
date: 2024-01-01
weight: 3
chapter: false
pre: "<b>5.3. </b>"
---

# Triển khai hạ tầng AWS

## Tổng quan

Trong phần này, chúng ta sẽ xây dựng toàn bộ hạ tầng AWS cho hệ thống **AI Supply Chain Control Tower**.

Các tài nguyên được triển khai sẽ đóng vai trò là nền tảng cho các thành phần Backend, AI Pipeline và Data Analytics ở các chương tiếp theo.

Kiến trúc được thiết kế theo mô hình **Cloud Native** nhằm đảm bảo:

- High Availability
- Scalability
- Security
- Cost Optimization

---

## Mục tiêu

Sau khi hoàn thành chương này bạn sẽ có thể:

- Tạo Amazon VPC
- Tạo Public và Private Subnets
- Cấu hình Security Groups
- Triển khai Amazon RDS PostgreSQL
- Tạo Amazon S3 Data Lake
- Tạo Amazon SQS Queue
- Tạo IAM Role cho Lambda

---

## Kiến trúc triển khai

Trong chương này chúng ta sẽ xây dựng các tài nguyên sau:

- Amazon VPC
- Public Subnet
- Private Subnet
- Security Group
- Amazon RDS PostgreSQL
- Amazon S3
- Amazon SQS
- IAM Role

> **Hình 5.3 - Infrastructure Architecture**

*(Chèn sơ đồ Infrastructure tại đây)*

---

## Nội dung

Trong chương này chúng ta sẽ thực hiện:

- 5.3.1 Tạo Amazon VPC
- 5.3.2 Tạo Public & Private Subnets
- 5.3.3 Tạo Security Groups
- 5.3.4 Triển khai Amazon RDS PostgreSQL
- 5.3.5 Tạo Amazon S3 Data Lake
- 5.3.6 Tạo Amazon SQS Queue
- 5.3.7 Tạo IAM Roles

---

## Kết quả

Sau khi hoàn thành chương này, toàn bộ hạ tầng AWS đã sẵn sàng để triển khai Backend và AI Pipeline.