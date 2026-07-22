---
title: "Các bài blogs đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

## Blog 1 - AWS Lambda – Chạy code mà không cần quản lý máy chủ

Bài viết giới thiệu AWS Lambda, một trong những dịch vụ Serverless tiêu biểu của AWS. Nội dung giải thích cách Lambda thực thi mã nguồn theo sự kiện mà không cần người dùng quản lý máy chủ hay hạ tầng. Bài viết trình bày kiến trúc hoạt động theo mô hình Event-driven, khả năng tự động mở rộng, cơ chế tính phí theo mức sử dụng thực tế và khả năng tích hợp với nhiều dịch vụ AWS như Amazon S3, Amazon API Gateway, Amazon DynamoDB, Amazon EventBridge và Amazon SQS. Ngoài ra, bài viết cũng giới thiệu các trường hợp sử dụng phổ biến, những lợi ích của kiến trúc Serverless và khi nào nên lựa chọn Amazon EC2 hoặc Amazon ECS thay vì AWS Lambda.

----

## Blog 2 - AWS Secrets Manager, Parameter Store hay AppConfig?

Bài viết này tìm hiểu ba dịch vụ của AWS giúp quản lý thông tin bí mật, cấu hình ứng dụng và thiết lập tính năng, bao gồm AWS Secrets Manager, AWS Systems Manager Parameter Store và AWS AppConfig. Nội dung giải thích mục đích sử dụng của từng dịch vụ, so sánh các tính năng, mô hình chi phí và những trường hợp sử dụng phổ biến. Qua đó, người đọc có thể hiểu rõ khi nào nên sử dụng từng dịch vụ để quản lý thông tin xác thực, dữ liệu cấu hình và triển khai ứng dụng một cách hiệu quả trên AWS.

----

## Blog 3 - AWS Organizations Security

Bài viết tập trung vào tầm quan trọng của việc bảo vệ AWS Organizations trước nguy cơ tài khoản bị rời khỏi tổ chức trái phép và duy trì tính bảo mật trong môi trường AWS nhiều tài khoản. Nội dung giới thiệu những rủi ro liên quan đến API `organizations:LeaveOrganization`, giải thích cách Service Control Policies (SCPs) giúp kiểm soát quyền truy cập, đồng thời chia sẻ các khuyến nghị từ AWS như áp dụng nguyên tắc Least Privilege, bảo vệ Root Account bằng xác thực đa yếu tố (MFA) và xây dựng quy trình Break Glass. Bài viết cũng tổng hợp những kiến thức quan trọng về quản trị, giám sát tập trung và quản lý chi phí trong AWS Organizations.

