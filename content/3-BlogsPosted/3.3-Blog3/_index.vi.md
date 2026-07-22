---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---
# AWS Organizations Security

### Bảo vệ AWS Organizations – Lớp bảo mật quan trọng nhưng dễ bị bỏ quên

Trong quá trình tìm hiểu về AWS Security, mình đã đọc một số bài viết từ AWS Security Blog và AWS Cloud Operations Blog về cách bảo vệ AWS Organizations khỏi việc tài khoản bị tự ý rời khỏi tổ chức.

Ban đầu mình nghĩ AWS Organizations chỉ là dịch vụ giúp quản lý nhiều tài khoản AWS. Tuy nhiên, sau khi tìm hiểu kỹ hơn, mình nhận ra đây còn là một thành phần quan trọng giúp tăng cường bảo mật và quản trị trong môi trường AWS Multi-Account.

## AWS Organizations là gì?

AWS Organizations là dịch vụ giúp quản lý nhiều tài khoản AWS trong cùng một tổ chức.

Thông qua dịch vụ này, quản trị viên có thể:

- Quản lý tập trung nhiều AWS Account.
- Áp dụng Service Control Policies (SCPs).
- Quản lý thanh toán tập trung (Consolidated Billing).
- Tổ chức tài khoản theo từng phòng ban hoặc môi trường như Development, Testing và Production.

Đây cũng là một trong những dịch vụ được AWS khuyến nghị khi triển khai kiến trúc Multi-Account.

---

## Vì sao cần bảo vệ AWS Organizations?

Theo AWS Customer Incident Response Team (AWS CIRT), sau khi chiếm được quyền truy cập vào một tài khoản thành viên, kẻ tấn công có thể tìm cách thực hiện hành động:

- `organizations:LeaveOrganization`

Nếu tài khoản rời khỏi Organizations, nhiều cơ chế quản lý và bảo mật ở cấp tổ chức sẽ không còn được áp dụng.

---

## Những ảnh hưởng khi tài khoản rời Organizations

### Service Control Policies (SCPs)

Khi tài khoản rời khỏi Organizations:

- Các Service Control Policies sẽ không còn hiệu lực.
- Có thể thực hiện những hành động trước đây bị SCP giới hạn.
- Tăng nguy cơ thay đổi hoặc tạo tài nguyên ngoài phạm vi quản lý.

---

### Giám sát tập trung

Nhiều doanh nghiệp sử dụng:

- AWS CloudTrail
- Amazon GuardDuty
- AWS Security Hub

để theo dõi toàn bộ môi trường AWS.

Nếu tài khoản bị tách khỏi Organizations, việc thu thập log và giám sát tập trung có thể không còn đầy đủ, gây khó khăn trong việc phát hiện các hoạt động bất thường.

---

### Quản lý chi phí

AWS Organizations còn hỗ trợ quản lý chi phí thông qua:

- Consolidated Billing
- AWS Budgets
- AWS Cost Explorer

Khi tài khoản hoạt động độc lập, việc theo dõi và kiểm soát chi phí sẽ trở nên khó khăn hơn, đặc biệt nếu tài khoản bị lạm dụng để tạo nhiều tài nguyên.

---

## Khuyến nghị từ AWS

### Chặn quyền LeaveOrganization

AWS khuyến nghị tạo Service Control Policy với quyền từ chối (Explicit Deny) đối với hành động:

- `organizations:LeaveOrganization`

Điều này giúp ngăn tài khoản thành viên tự ý rời khỏi Organizations.

---

### Áp dụng nguyên tắc Least Privilege

Chỉ cấp những quyền thực sự cần thiết cho người dùng hoặc ứng dụng.

Đồng thời hạn chế các quyền nhạy cảm như:

- `iam:AttachRolePolicy`
- `iam:PutRolePolicy`
- `iam:AttachUserPolicy`
- `sts:AssumeRole`

Việc giới hạn quyền sẽ giảm thiểu rủi ro nếu tài khoản bị xâm nhập.

---

### Bảo vệ Root Account

AWS cũng khuyến nghị:

- Bật MFA cho Root Account.
- Không sử dụng Root cho công việc hằng ngày.
- Xóa Root Access Key nếu không cần thiết.
- Quản lý Root Account theo quy trình tập trung.

Đây vẫn là một trong những lớp bảo vệ quan trọng nhất của môi trường AWS.

---

### Thiết lập quy trình Break Glass

Đối với các trường hợp đặc biệt, AWS khuyến nghị xây dựng quy trình Break Glass để xử lý các thao tác khẩn cấp thay vì cho phép tài khoản tự ý rời khỏi Organizations.

Điều này giúp cân bằng giữa tính linh hoạt và mức độ an toàn của hệ thống.

---

## Điều mình rút ra

Sau khi tìm hiểu, mình nhận thấy AWS Organizations không chỉ giúp quản lý nhiều tài khoản mà còn đóng vai trò quan trọng trong việc bảo vệ toàn bộ môi trường AWS.

Nếu lớp quản trị này bị ảnh hưởng, nhiều cơ chế bảo mật khác như SCP, giám sát tập trung và quản lý chi phí cũng sẽ bị tác động theo.

---

## Kết luận

AWS Organizations là nền tảng quan trọng trong mô hình Multi-Account trên AWS.

Việc áp dụng Service Control Policies, thực hiện nguyên tắc Least Privilege, bảo vệ Root Account và xây dựng quy trình Break Glass sẽ giúp nâng cao khả năng bảo mật và quản trị cho toàn bộ hệ thống.

Hiểu rõ vai trò của AWS Organizations sẽ giúp xây dựng môi trường AWS an toàn và hiệu quả hơn.

---

## Nguồn tham khảo

- AWS Security Blog – CIRT insights: How to help prevent unauthorized account removals from AWS Organizations  
  https://aws.amazon.com/blogs/security/cirt-insights-how-to-help-prevent-unauthorized-account-removals-from-aws-organizations/

- AWS Cloud Operations Blog – Essential security controls to prevent unauthorized account removal in AWS Organizations  
  https://aws.amazon.com/blogs/mt/essential-security-controls-to-prevent-unauthorized-account-removal-in-aws-organizations/

---

[#AWS](https://www.facebook.com/hashtag/aws)  
[#AWSOrganizations](https://www.facebook.com/hashtag/awsorganizations)  
[#AWSSecurity](https://www.facebook.com/hashtag/awssecurity)  
[#CloudSecurity](https://www.facebook.com/hashtag/cloudsecurity)  
[#AWSCIRT](https://www.facebook.com/hashtag/awscirt)  
[#CloudComputing](https://www.facebook.com/hashtag/cloudcomputing)  
[#AmazonWebServices](https://www.facebook.com/hashtag/amazonwebservices)  
[#LearningAWS](https://www.facebook.com/hashtag/learningaws)  
[#CyberSecurity](https://www.facebook.com/hashtag/cybersecurity)