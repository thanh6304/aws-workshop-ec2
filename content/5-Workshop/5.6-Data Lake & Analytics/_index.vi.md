---
title: "5.6 Data Lake và Analytics"
date: 2024-01-01
weight: 6
chapter: false
---

## Tổng quan

Trong chương này, chúng ta sẽ hoàn thiện lớp Data Lake và Analytics cho hệ thống **AI Supply Chain Control Tower**.

Dữ liệu tồn kho, đơn hàng, vận chuyển và báo cáo sẽ được lưu trữ tập trung trong Amazon S3. AWS Glue Data Catalog quản lý metadata của các tập dữ liệu, trong khi Amazon Athena cho phép truy vấn trực tiếp dữ liệu bằng SQL mà không cần triển khai máy chủ cơ sở dữ liệu phân tích riêng.

Dữ liệu cũng sẽ được tối ưu bằng định dạng Apache Parquet và cơ chế phân vùng theo thời gian hoặc kho hàng. Việc này giúp Amazon Athena chỉ đọc những cột và phân vùng cần thiết, từ đó giảm lượng dữ liệu quét, cải thiện hiệu năng và tối ưu chi phí truy vấn.

---

## Kiến trúc

```text
Operational Systems
        │
        ▼
Amazon S3 Data Lake
        │
        ├── Raw Zone
        ├── Processed Zone
        └── Analytics Zone
                │
                ▼
       AWS Glue Data Catalog
                │
                ▼
          Amazon Athena
                │
        ┌───────┴────────┐
        ▼                ▼
 AI Worker Lambda     Dashboard
        │
        ▼
 Amazon Bedrock
```

---

## Mục tiêu

Sau khi hoàn thành chương này, bạn sẽ:

- Tổ chức dữ liệu trong Amazon S3 theo kiến trúc Data Lake.
- Phân chia dữ liệu thành Raw, Processed và Analytics Zone.
- Chuyển dữ liệu CSV sang định dạng Apache Parquet.
- Phân vùng dữ liệu theo ngày, tháng hoặc kho hàng.
- Cập nhật metadata trong AWS Glue Data Catalog.
- Tạo Athena Workgroup và vị trí lưu kết quả truy vấn.
- Xây dựng các truy vấn phân tích chuỗi cung ứng.
- Tạo View phục vụ Dashboard.
- Kiểm tra hiệu năng và lượng dữ liệu được Athena quét.

---

## Nội dung chương

### 5.6.1 Tổ chức Amazon S3 Data Lake

Thiết kế cấu trúc thư mục và phân chia dữ liệu thành các vùng:

```text
raw/
processed/
analytics/
athena-results/
```

### 5.6.2 Tải dữ liệu chuỗi cung ứng lên Amazon S3

Chuẩn bị và tải các tập dữ liệu:

```text
inventory
orders
shipments
suppliers
```

### 5.6.3 Chuyển dữ liệu sang định dạng Apache Parquet

Sử dụng Athena CTAS để chuyển dữ liệu CSV sang định dạng cột Apache Parquet.

### 5.6.4 Phân vùng dữ liệu

Tổ chức dữ liệu theo các Partition Key như:

```text
year
month
day
warehouse_id
```

### 5.6.5 Cập nhật AWS Glue Data Catalog

Cập nhật Schema, Table và Partition để Athena có thể truy vấn dữ liệu đã tối ưu.

### 5.6.6 Tạo Athena Workgroup

Thiết lập Workgroup riêng cho workshop, cấu hình vị trí lưu kết quả và giới hạn lượng dữ liệu quét.

### 5.6.7 Xây dựng truy vấn phân tích

Tạo truy vấn cho:

- Tồn kho thấp.
- Đơn hàng theo trạng thái.
- Doanh thu theo thời gian.
- Vận chuyển bị trễ.
- Hiệu suất nhà cung cấp.
- Xu hướng nhu cầu.

### 5.6.8 Tạo View phục vụ Dashboard

Tạo các Athena View để Backend và Dashboard sử dụng lại các truy vấn đã chuẩn hóa.

### 5.6.9 Kiểm thử Data Lake và Analytics

Kiểm tra dữ liệu, Glue Catalog, Athena Query, Partition và kết quả trả về cho Dashboard.

---

## Luồng xử lý dữ liệu

```text
CSV / JSON Data
       │
       ▼
Amazon S3 Raw Zone
       │
       ▼
Athena CTAS
       │
       ▼
Amazon S3 Processed Zone
       │
       ▼
AWS Glue Data Catalog
       │
       ▼
Amazon Athena
       │
       ├── Dashboard Queries
       └── AI Analysis Queries
```

---

## Kết quả mong đợi

Sau khi hoàn thành chương này:

- Dữ liệu chuỗi cung ứng được lưu tập trung trong Amazon S3.
- Dữ liệu được tổ chức thành các vùng rõ ràng.
- Dữ liệu phân tích được lưu ở định dạng Apache Parquet.
- Các bảng được phân vùng để giảm dữ liệu quét.
- AWS Glue Data Catalog quản lý đầy đủ metadata.
- Amazon Athena có thể truy vấn dữ liệu bằng SQL.
- Dashboard có thể sử dụng các View phân tích.
- AI Worker Lambda có thể lấy dữ liệu đã tổng hợp để gửi đến Amazon Bedrock.
