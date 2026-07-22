---
title: "5.6.1 Tổ chức Amazon S3 Data Lake"
date: 2024-01-01
weight: 1
chapter: false
---

# 5.6.1 Tổ chức Amazon S3 Data Lake

## Tổng quan

Amazon S3 đóng vai trò là kho lưu trữ dữ liệu tập trung của hệ thống **AI Supply Chain Control Tower**.

Thay vì lưu tất cả file trong cùng một thư mục, Data Lake sẽ được chia thành nhiều vùng dựa trên trạng thái và mục đích sử dụng của dữ liệu.

Trong workshop này, chúng ta sử dụng ba vùng chính:

```text
Raw Zone
Processed Zone
Analytics Zone
```

Ngoài ra, một thư mục riêng được sử dụng để lưu kết quả truy vấn Amazon Athena.

---

## Kiến trúc lưu trữ

```text
Amazon S3 Data Lake
│
├── raw/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── processed/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── analytics/
│   ├── inventory-summary/
│   ├── order-summary/
│   ├── shipment-summary/
│   └── supplier-performance/
│
└── athena-results/
```

---

## Mục tiêu

Sau bài này bạn sẽ:

- Hiểu vai trò của từng vùng dữ liệu.
- Tạo cấu trúc thư mục cho Data Lake.
- Tách biệt dữ liệu gốc và dữ liệu đã xử lý.
- Chuẩn bị vị trí lưu kết quả Amazon Athena.
- Thiết lập các cấu hình bảo mật cơ bản cho S3 Bucket.

---

## Phân loại các vùng dữ liệu

### Raw Zone

Raw Zone lưu dữ liệu gốc được tải lên từ các hệ thống nguồn.

Ví dụ:

```text
CSV
JSON
Application exports
Supplier files
Warehouse reports
```

Dữ liệu trong Raw Zone không nên bị chỉnh sửa hoặc ghi đè sau khi tải lên.

Ví dụ đường dẫn:

```text
s3://ai-supplychain-datalake/raw/inventory/
s3://ai-supplychain-datalake/raw/orders/
s3://ai-supplychain-datalake/raw/shipments/
s3://ai-supplychain-datalake/raw/suppliers/
```

### Processed Zone

Processed Zone lưu dữ liệu đã được:

- Làm sạch.
- Chuẩn hóa.
- Chuyển đổi kiểu dữ liệu.
- Loại bỏ bản ghi lỗi hoặc trùng lặp.
- Chuyển sang định dạng Apache Parquet.
- Phân vùng để tối ưu truy vấn.

Ví dụ:

```text
s3://ai-supplychain-datalake/processed/orders/
```

### Analytics Zone

Analytics Zone lưu dữ liệu đã được tổng hợp để phục vụ:

- Dashboard.
- Báo cáo quản trị.
- Phân tích AI.
- Theo dõi KPI.
- Truy vấn thường xuyên.

Ví dụ:

```text
s3://ai-supplychain-datalake/analytics/inventory-summary/
```

### Athena Results

Thư mục này lưu kết quả của các truy vấn Amazon Athena.

```text
s3://ai-supplychain-datalake/athena-results/
```

Không nên lưu dữ liệu nghiệp vụ vào thư mục này.

---

## Bước 1. Mở Amazon S3 Console

Đăng nhập AWS Management Console.

Tìm kiếm:

```text
Amazon S3
```

Chọn Bucket:

```text
ai-supplychain-datalake
```

Bucket này đã được tạo trong phần triển khai hạ tầng.

---

## Bước 2. Tạo thư mục Raw Zone

Chọn:

```text
Create folder
```

Tạo thư mục:

```text
raw
```

Bên trong `raw`, tạo các thư mục:

```text
inventory
orders
shipments
suppliers
```

Cấu trúc sau khi hoàn thành:

```text
raw/
├── inventory/
├── orders/
├── shipments/
└── suppliers/
```

---

## Bước 3. Tạo thư mục Processed Zone

Tạo thư mục:

```text
processed
```

Bên trong tạo:

```text
inventory
orders
shipments
suppliers
```

Cấu trúc:

```text
processed/
├── inventory/
├── orders/
├── shipments/
└── suppliers/
```

---

## Bước 4. Tạo thư mục Analytics Zone

Tạo thư mục:

```text
analytics
```

Bên trong tạo:

```text
inventory-summary
order-summary
shipment-summary
supplier-performance
```

Cấu trúc:

```text
analytics/
├── inventory-summary/
├── order-summary/
├── shipment-summary/
└── supplier-performance/
```

---

## Bước 5. Tạo thư mục lưu kết quả Athena

Tạo thư mục:

```text
athena-results
```

Đường dẫn đầy đủ:

```text
s3://ai-supplychain-datalake/athena-results/
```

Vị trí này sẽ được sử dụng khi cấu hình Amazon Athena Workgroup.

---

## Bước 6. Bật S3 Versioning

Trong S3 Bucket, chọn:

```text
Properties
```

Tìm:

```text
Bucket Versioning
```

Chọn:

```text
Edit
```

Sau đó chọn:

```text
Enable
```

S3 Versioning giúp bảo vệ dữ liệu trước thao tác ghi đè hoặc xóa nhầm.

---

## Bước 7. Kiểm tra Block Public Access

Chọn:

```text
Permissions
```

Trong phần:

```text
Block public access
```

Đảm bảo:

```text
Block all public access: On
```

Dữ liệu chuỗi cung ứng không được phép truy cập công khai.

---

## Bước 8. Kiểm tra mã hóa dữ liệu

Trong phần:

```text
Properties
```

Tìm:

```text
Default encryption
```

Có thể sử dụng:

```text
SSE-S3
```

hoặc:

```text
SSE-KMS
```

Đối với môi trường Production, nên sử dụng AWS KMS để kiểm soát quyền sử dụng khóa mã hóa chi tiết hơn.

---

## Bước 9. Kiểm tra cấu trúc Data Lake

Sau khi hoàn thành, Bucket phải có cấu trúc:

```text
ai-supplychain-datalake/
│
├── raw/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── processed/
│   ├── inventory/
│   ├── orders/
│   ├── shipments/
│   └── suppliers/
│
├── analytics/
│   ├── inventory-summary/
│   ├── order-summary/
│   ├── shipment-summary/
│   └── supplier-performance/
│
└── athena-results/
```

---

## Quy tắc đặt tên file

Nên đặt tên file theo cấu trúc:

```text
<dataset>_<date>_<sequence>.<extension>
```

Ví dụ:

```text
inventory_20260722_001.csv
orders_20260722_001.csv
shipments_20260722_001.csv
suppliers_20260722_001.csv
```

Không nên sử dụng:

- Khoảng trắng.
- Ký tự đặc biệt.
- Tên file không thể hiện nội dung.
- Tên như `data.csv`, `new.csv` hoặc `final-final.csv`.

---

## Quy tắc phân vùng đề xuất

Khi dữ liệu được chuyển sang Processed Zone, có thể sử dụng cấu trúc:

```text
processed/orders/
└── year=2026/
    └── month=07/
        └── day=22/
            └── orders.parquet
```

Đối với dữ liệu tồn kho:

```text
processed/inventory/
└── warehouse_id=WH001/
    └── year=2026/
        └── month=07/
            └── inventory.parquet
```

Amazon Athena có thể bỏ qua những phân vùng không phù hợp với điều kiện truy vấn, giúp giảm lượng dữ liệu cần đọc. AWS khuyến nghị lựa chọn Partition Key dựa trên những điều kiện lọc được sử dụng thường xuyên, đồng thời tránh tạo quá nhiều phân vùng hoặc file quá nhỏ.

---

## Best Practices

- Không chỉnh sửa trực tiếp dữ liệu trong Raw Zone.
- Không cho phép truy cập công khai S3 Bucket.
- Bật mã hóa mặc định.
- Bật Versioning cho dữ liệu quan trọng.
- Sử dụng tên file và thư mục nhất quán.
- Tách dữ liệu gốc và dữ liệu đã xử lý.
- Không lưu file tạm vào Analytics Zone.
- Tránh tạo quá nhiều file nhỏ.
- Phân vùng theo các trường thường được sử dụng trong điều kiện `WHERE`.
- Thiết lập Lifecycle Rule khi dữ liệu tăng trưởng lớn.

---

## Kiểm tra kết quả

Đảm bảo:

- Bucket không được truy cập công khai.
- Raw Zone đã được tạo.
- Processed Zone đã được tạo.
- Analytics Zone đã được tạo.
- Athena Results đã được tạo.
- Versioning đã được bật.
- Default Encryption đã được cấu hình.
- Các thư mục sử dụng quy tắc đặt tên nhất quán.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Thiết kế cấu trúc Amazon S3 Data Lake.
- Phân chia dữ liệu theo Raw, Processed và Analytics Zone.
- Chuẩn bị vị trí lưu kết quả Amazon Athena.
- Thiết lập các cấu hình bảo vệ dữ liệu cơ bản.
- Chuẩn bị nền tảng cho việc tải và tối ưu dữ liệu chuỗi cung ứng.
