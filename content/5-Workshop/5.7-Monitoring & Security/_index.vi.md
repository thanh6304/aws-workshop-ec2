---
title: "5.7 Monitoring & Security"
date: 2024-01-01
weight: 7
chapter: true
pre: "<b>5.7. </b>"
---

## Giới thiệu

Sau khi triển khai thành công hệ thống **AI Supply Chain Control Tower**, việc giám sát hoạt động, phát hiện sự cố và bảo vệ tài nguyên AWS là một phần không thể thiếu để đảm bảo hệ thống luôn sẵn sàng và an toàn.

Trong chương này, bạn sẽ tìm hiểu cách sử dụng các dịch vụ của AWS để theo dõi hiệu năng hệ thống, cấu hình cảnh báo, quản lý quyền truy cập, bảo vệ thông tin nhạy cảm và ghi nhận các hoạt động trong tài khoản AWS.

Các nội dung trong chương bao gồm:

- Giám sát hệ thống bằng Amazon CloudWatch.
- Cấu hình CloudWatch Alarms.
- Gửi thông báo qua Amazon SNS.
- Quản lý quyền truy cập bằng AWS IAM.
- Lưu trữ thông tin bí mật với AWS Secrets Manager.
- Mã hóa dữ liệu bằng AWS KMS.
- Theo dõi hoạt động tài khoản với AWS CloudTrail.
- Kiểm tra và xác thực toàn bộ hệ thống.

---

## Kiến trúc

```text
                    AI Supply Chain Control Tower
                               │
        ┌──────────────────────┼──────────────────────┐
        ▼                      ▼                      ▼
   API Gateway             Lambda API          Lambda AI Worker
        │                      │                      │
        └──────────────┬───────┴──────────────┬───────┘
                       ▼                      ▼
                  Amazon CloudWatch      Amazon CloudTrail
                       │                      │
        ┌──────────────┼──────────────┐       │
        ▼              ▼              ▼       ▼
     Metrics         Logs         Alarms   Audit Logs
        │                             │
        ▼                             ▼
 Amazon SNS                     Email Notification
        │
        ▼
    Administrator

Security Services

IAM
Secrets Manager
AWS KMS
```

---

## Mục tiêu

Sau khi hoàn thành chương này, bạn sẽ có thể:

- Giám sát hoạt động của hệ thống bằng Amazon CloudWatch.
- Thu thập và phân tích Metrics cũng như Logs.
- Cấu hình CloudWatch Alarms để phát hiện sự cố.
- Gửi cảnh báo tự động bằng Amazon SNS.
- Quản lý quyền truy cập với AWS IAM.
- Bảo vệ thông tin nhạy cảm bằng AWS Secrets Manager.
- Mã hóa dữ liệu bằng AWS Key Management Service (AWS KMS).
- Theo dõi hoạt động tài khoản bằng AWS CloudTrail.
- Xác thực và đánh giá toàn bộ hệ thống sau khi triển khai.

---

## Nội dung

Chương này gồm các bài thực hành sau:

- **5.7.1** Monitor the System with Amazon CloudWatch
- **5.7.2** Configure CloudWatch Alarms
- **5.7.3** Configure Amazon SNS Notifications
- **5.7.4** Secure Access with AWS Identity and Access Management (IAM)
- **5.7.5** Protect Secrets with AWS Secrets Manager
- **5.7.6** Encrypt Data Using AWS Key Management Service (AWS KMS)
- **5.7.7** Audit AWS Resources with AWS CloudTrail
- **5.7.8** Validate Monitoring and Security Configuration

---

## Kết quả mong đợi

Sau khi hoàn thành chương này, hệ thống AI Supply Chain Control Tower sẽ có khả năng:

- Giám sát hiệu năng theo thời gian thực.
- Ghi nhận và lưu trữ nhật ký hoạt động.
- Phát hiện sự cố và gửi cảnh báo tự động.
- Bảo vệ dữ liệu và thông tin nhạy cảm.
- Quản lý quyền truy cập theo nguyên tắc Least Privilege.
- Theo dõi các hoạt động trong tài khoản AWS để phục vụ kiểm toán và điều tra sự cố.
