---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# AWS Lambda – Chạy code mà không cần quản lý máy chủ

Xin chào mọi người!

Trong quá trình tìm hiểu về **Amazon Web Services (AWS)**, mình có cơ hội tìm hiểu về **AWS Lambda** – một trong những dịch vụ Serverless phổ biến nhất hiện nay.

Trước đây mình luôn nghĩ rằng muốn triển khai ứng dụng thì phải tạo máy chủ (Amazon EC2), cài đặt hệ điều hành, web server và quản lý tài nguyên. Tuy nhiên, AWS Lambda đã thay đổi hoàn toàn cách tiếp cận này khi cho phép chúng ta **chạy code mà không cần quản lý máy chủ**.

---

## AWS Lambda là gì?

AWS Lambda là dịch vụ **Serverless Computing** của AWS, cho phép chạy code theo sự kiện mà không cần tự quản lý hạ tầng.

Thay vì phải:

- Khởi tạo EC2
- Cài đặt hệ điều hành
- Cấu hình Web Server
- Theo dõi CPU, RAM
- Thiết lập Auto Scaling

Bạn chỉ cần:

- Viết code
- Upload lên AWS
- Khai báo sự kiện kích hoạt (Trigger)

AWS sẽ tự động thực hiện toàn bộ phần còn lại.

---

## AWS Lambda hoạt động như thế nào?

Lambda chỉ thực thi khi có **Event** xảy ra.

Một số Trigger phổ biến:

- Người dùng upload file lên **Amazon S3**
- API Gateway nhận HTTP Request
- DynamoDB có dữ liệu mới
- Amazon EventBridge chạy theo lịch
- Amazon SQS nhận Message

Khi sự kiện xảy ra:

1. Lambda được kích hoạt.
2. AWS tự động cấp tài nguyên.
3. Hàm được thực thi.
4. Trả kết quả.
5. Giải phóng tài nguyên sau khi hoàn thành.

Điều này giúp tối ưu chi phí vì tài nguyên chỉ được sử dụng khi thật sự cần thiết.

---

## Những ưu điểm nổi bật của AWS Lambda

### Không cần quản lý Server

Đây là ưu điểm lớn nhất của Lambda.

AWS sẽ tự động:

- Quản lý máy chủ
- Vá lỗi hệ điều hành
- Theo dõi tài nguyên
- Quản lý bảo mật
- Tự động mở rộng

Lập trình viên chỉ cần tập trung vào logic của ứng dụng.

---

### Tự động mở rộng (Auto Scaling)

Nếu cùng lúc có hàng nghìn request gửi đến, Lambda sẽ tự động tạo nhiều phiên thực thi để xử lý.

Khi lưu lượng giảm xuống, các phiên thực thi cũng tự động giảm theo.

Không cần cấu hình Auto Scaling như khi sử dụng Amazon EC2.

---

### Chỉ trả tiền khi sử dụng

Một điểm mình rất thích là Lambda chỉ tính phí khi code được thực thi.

Nếu không có request nào gửi đến thì gần như không phát sinh chi phí tính toán.

Đây là lựa chọn phù hợp cho:

- Startup
- Dự án nhỏ
- Ứng dụng có lượng truy cập không thường xuyên

---

## AWS Lambda thường được sử dụng để làm gì?

Một số trường hợp sử dụng phổ biến:

- Xử lý hình ảnh sau khi upload lên Amazon S3
- Xây dựng REST API cùng Amazon API Gateway
- Gửi Email tự động
- Gửi Notification
- Xử lý dữ liệu thời gian thực
- Tự động hóa tác vụ trên AWS
- Phân tích Log từ Amazon CloudWatch

Nhờ khả năng tích hợp với rất nhiều dịch vụ AWS, Lambda trở thành thành phần quan trọng trong kiến trúc **Serverless**.

---

## Những điều mình học được

Sau khi tìm hiểu AWS Lambda, mình rút ra một số điều:

- Không phải ứng dụng nào cũng cần triển khai trên EC2.
- Serverless giúp giảm đáng kể công việc quản trị hạ tầng.
- Lambda đặc biệt phù hợp với các tác vụ ngắn và xử lý theo sự kiện.
- Kết hợp Lambda với Amazon S3, API Gateway và DynamoDB sẽ tạo nên những ứng dụng linh hoạt, dễ mở rộng.

Bên cạnh đó, mình cũng nhận thấy Lambda không phải là giải pháp cho mọi bài toán. Với các ứng dụng cần xử lý lâu hoặc yêu cầu kiểm soát hệ điều hành, EC2 hoặc ECS vẫn sẽ phù hợp hơn.

---

## Kết luận

AWS Lambda là một trong những dịch vụ tiêu biểu của mô hình **Serverless Computing** trên AWS.

Dịch vụ này giúp:

- Triển khai ứng dụng nhanh hơn
- Không cần quản lý máy chủ
- Tự động mở rộng
- Chỉ trả phí theo mức sử dụng thực tế

Theo mình, đây là một dịch vụ rất đáng để tìm hiểu đối với những ai mới bắt đầu học AWS vì nó thể hiện rõ xu hướng phát triển của các hệ thống Cloud hiện đại.

---

## Hình ảnh minh họa

*(Chèn hình ảnh kiến trúc AWS Lambda hoặc sơ đồ Serverless tại đây.)*

---

## Tài liệu tham khảo

- AWS Lambda Documentation: https://docs.aws.amazon.com/lambda/
- AWS Lambda Developer Guide: https://docs.aws.amazon.com/lambda/latest/dg/welcome.html
- AWS Serverless Overview: https://aws.amazon.com/serverless/

---

## Bài viết liên quan

Nếu bạn quan tâm đến AWS Serverless, có thể tìm hiểu thêm:

- Amazon API Gateway
- Amazon EventBridge
- Amazon DynamoDB
- Amazon CloudFront

---

[#AWS](https://www.facebook.com/hashtag/aws)
[#AWSLambda](https://www.facebook.com/hashtag/awslambda)
[#Serverless](https://www.facebook.com/hashtag/serverless)
[#CloudComputing](https://www.facebook.com/hashtag/cloudcomputing)
[#AmazonWebServices](https://www.facebook.com/hashtag/amazonwebservices)
[#LearningAWS](https://www.facebook.com/hashtag/learningaws)
[#CloudTechnology](https://www.facebook.com/hashtag/cloudtechnology)
[#AWSCommunity](https://www.facebook.com/hashtag/awscommunity)