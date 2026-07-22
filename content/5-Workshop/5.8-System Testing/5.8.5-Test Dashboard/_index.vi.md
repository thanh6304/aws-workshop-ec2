---
title: "5.8.5 Kiểm thử Dashboard"
date: 2024-01-01
weight: 5
chapter: false
---



## Tổng quan

Dashboard là giao diện chính của AI Supply Chain Control Tower, cung cấp thông tin tổng quan về chuỗi cung ứng và các kết quả phân tích AI.

Trong bài này, bạn sẽ kiểm tra rằng Dashboard hiển thị đúng dữ liệu từ Backend API và các kết quả phân tích được tạo bởi AI Pipeline.

---

## Kiến trúc

```text
Amazon RDS
      │
      ▼
Backend API
      │
      ▼
API Gateway
      │
      ▼
Frontend (AWS Amplify)
      │
      ▼
Dashboard
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Kiểm tra Dashboard.
- Kiểm tra các chỉ số KPI.
- Kiểm tra dữ liệu AI Insights.
- Kiểm tra khả năng làm mới dữ liệu.

---

# Bước 1. Mở Dashboard

Truy cập ứng dụng đã triển khai bằng AWS Amplify.

Đăng nhập bằng tài khoản hợp lệ.

Sau khi đăng nhập thành công, Dashboard sẽ được hiển thị.

---

# Bước 2. Kiểm tra KPI

Xác nhận Dashboard hiển thị các chỉ số:

```text
Total Orders

Inventory Level

Active Shipments

Suppliers

Delayed Orders
```

Đối chiếu các giá trị với dữ liệu trong Amazon RDS hoặc kết quả truy vấn Amazon Athena.

---

# Bước 3. Kiểm tra danh sách dữ liệu

Kiểm tra các bảng trên Dashboard, ví dụ:

```text
Inventory

Orders

Shipments

Suppliers
```

Đảm bảo:

- Dữ liệu được tải thành công.
- Không có bản ghi bị thiếu.
- Thông tin hiển thị đúng với dữ liệu lưu trong cơ sở dữ liệu.

---

# Bước 4. Kiểm tra AI Insights

Mở khu vực:

```text
AI Insights
```

Xác nhận các kết quả phân tích như:

```text
Low Stock Prediction

Demand Forecast

Supplier Performance

Shipment Delay Analysis
```

Đảm bảo nội dung được sinh bởi Amazon Bedrock và phản ánh dữ liệu hiện tại.

---

# Bước 5. Kiểm tra khả năng làm mới dữ liệu

Tạo một đơn hàng mới thông qua Backend API.

Sau khi AI Pipeline hoàn tất xử lý:

- Làm mới Dashboard.
- Xác nhận KPI và bảng dữ liệu đã được cập nhật.
- Kiểm tra AI Insights có phản ánh dữ liệu mới.

---

# Bước 6. Kiểm tra xử lý lỗi

Mô phỏng Backend API tạm thời không khả dụng hoặc trả về lỗi.

Đảm bảo Dashboard:

- Hiển thị thông báo lỗi thân thiện.
- Không bị treo hoặc hiển thị trang trắng.
- Cho phép người dùng thử tải lại dữ liệu.

---

# Bước 7. Kiểm tra trên nhiều thiết bị

Mở Dashboard trên:

- Máy tính để bàn.
- Máy tính bảng.
- Điện thoại.

Đảm bảo:

- Giao diện responsive.
- Không có thành phần bị chồng lấn.
- Các biểu đồ và bảng dữ liệu hiển thị đầy đủ.

---

# Bước 8. Kiểm tra hiệu năng

Quan sát:

- Thời gian tải Dashboard.
- Thời gian phản hồi của Backend API.
- Thời gian hiển thị AI Insights.

Đảm bảo trải nghiệm người dùng ổn định và không có độ trễ đáng kể.

---

## Best Practices

- Chỉ tải những dữ liệu cần thiết khi khởi tạo Dashboard.
- Sử dụng phân trang hoặc lazy loading cho các bảng dữ liệu lớn.
- Hiển thị trạng thái Loading khi đang tải dữ liệu.
- Hiển thị thông báo lỗi rõ ràng nếu không thể lấy dữ liệu.
- Đồng bộ Dashboard với dữ liệu mới sau khi AI Pipeline hoàn tất.

---

## Kiểm tra kết quả

Đảm bảo:

- Dashboard hiển thị đầy đủ KPI.
- Các bảng dữ liệu được tải chính xác.
- AI Insights hiển thị đúng kết quả phân tích.
- Dashboard cập nhật dữ liệu sau khi có thay đổi.
- Giao diện hoạt động tốt trên nhiều thiết bị.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Xác nhận Dashboard hiển thị dữ liệu chính xác.
- Kiểm tra khả năng cập nhật dữ liệu theo thời gian thực hoặc theo chu kỳ làm mới.
- Xác minh kết quả phân tích AI được hiển thị cho người dùng cuối.
- Đảm bảo giao diện đáp ứng yêu cầu sử dụng trong môi trường thực tế.
