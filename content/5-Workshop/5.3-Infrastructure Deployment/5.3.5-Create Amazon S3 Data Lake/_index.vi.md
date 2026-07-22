---
title: "5.3.5 Tạo Amazon S3 Data Lake"
date: 2024-01-01
weight: 5
chapter: false
---


## Tổng quan

Amazon Simple Storage Service (Amazon S3) là dịch vụ lưu trữ đối tượng (Object Storage) của AWS với khả năng mở rộng gần như không giới hạn.

Trong hệ thống **AI Supply Chain Control Tower**, Amazon S3 đóng vai trò là **Data Lake**, lưu trữ toàn bộ dữ liệu phục vụ cho phân tích và trí tuệ nhân tạo.

Các dữ liệu được lưu trữ bao gồm:

- Dữ liệu đơn hàng
- Dữ liệu tồn kho
- Dữ liệu vận chuyển
- Báo cáo CSV
- File JSON
- Kết quả AI
- Nhật ký hệ thống

Amazon Athena sẽ truy vấn trực tiếp dữ liệu trong S3 thông qua AWS Glue Data Catalog mà không cần tải dữ liệu vào cơ sở dữ liệu.

---

# Kiến trúc

```
Business Data
        │
        ▼
Amazon S3 Data Lake
        │
        ├────────► AWS Glue Data Catalog
        │
        ▼
Amazon Athena
        │
        ▼
Lambda Worker
        │
        ▼
Amazon Bedrock
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo S3 Bucket
- Cấu hình Block Public Access
- Bật Versioning
- Tạo cấu trúc thư mục
- Upload dữ liệu mẫu

---

# Các bước thực hiện

## Bước 1. Mở Amazon S3

Đăng nhập AWS Console.

Tìm kiếm:

```
S3
```

Chọn:

```
Amazon S3
```

![Kiến trúc S3](/images/5-Workshop/5.3-S3-vpc/5.3.5.png)

---

## Bước 2. Tạo Bucket

Chọn

```
Create bucket
```

---

## Bước 3. Cấu hình Bucket

Điền thông tin:

| Thuộc tính          | Giá trị                 |
| ------------------- | ----------------------- |
| Bucket Name         | ai-supplychain-datalake |
| Region              | ap-southeast-1          |
| Object Ownership    | ACLs disabled           |
| Block Public Access | Enable                  |

> Chèn hình: Create Bucket

---

## Bước 4. Bật Versioning

Trong mục

```
Bucket Versioning
```

Chọn

```
Enable
```

Versioning giúp khôi phục dữ liệu khi bị ghi đè hoặc xóa nhầm.

---

## Bước 5. Tạo cấu trúc thư mục

Tạo các thư mục:

```
inventory/

orders/

shipments/

reports/

analytics/

ai-results/

logs/
```

> Chèn hình: Folder Structure

---

## Bước 6. Upload dữ liệu mẫu

Upload một số file CSV hoặc JSON:

```
inventory.csv

orders.csv

shipment.csv
```

Các file này sẽ được Athena sử dụng trong chương tiếp theo.

---

## Bước 7. Kiểm tra Bucket

Bucket sẽ hiển thị:

```
inventory/

orders/

shipments/

reports/

analytics/

ai-results/

logs/
```

---

# Best Practices

AWS khuyến nghị:

- Luôn bật Block Public Access.
- Không lưu dữ liệu nhạy cảm ở chế độ Public.
- Sử dụng Versioning.
- Sử dụng thư mục rõ ràng để Glue dễ quản lý.
- Mã hóa dữ liệu bằng AWS KMS khi triển khai Production.

---

# Kết quả

Sau bài này bạn đã:

- Tạo thành công Amazon S3 Data Lake.
- Upload dữ liệu mẫu.
- Chuẩn bị dữ liệu cho AWS Glue và Amazon Athena.
- Hoàn thiện tầng lưu trữ dữ liệu của hệ thống.
