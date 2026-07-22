---
title: "5.6.6 Tạo Amazon Athena Workgroup"
date: 2024-01-01
weight: 6
chapter: false
---


## Tổng quan

Amazon Athena hỗ trợ **Workgroup** để quản lý người dùng, kiểm soát chi phí và cấu hình truy vấn.

Mỗi Workgroup có thể cấu hình riêng:

- Output Location
- Encryption
- Query Limits
- Metrics
- CloudWatch
- IAM Permissions

Trong workshop này, chúng ta sẽ tạo Workgroup dành riêng cho hệ thống AI Supply Chain.

---

## Kiến trúc

```text
User
 │
 ▼
Athena Workgroup
 │
 ├── Query Engine
 ├── Query History
 ├── CloudWatch Metrics
 ├── Result Configuration
 │
 ▼
Amazon S3
athena-results/
```

---

## Mục tiêu

Sau bài này bạn sẽ:

- Tạo Workgroup mới.
- Thiết lập Output Location.
- Bật mã hóa kết quả truy vấn.
- Kiểm tra lịch sử truy vấn.
- Chuẩn bị môi trường phân tích dữ liệu.

---

# Bước 1. Mở Amazon Athena

Đăng nhập AWS Console.

Tìm:

```text
Amazon Athena
```

---

# Bước 2. Mở Workgroups

Chọn:

```text
Workgroups
```

Chọn:

```text
Create workgroup
```

---

# Bước 3. Nhập thông tin

Tên:

```text
SupplyChainAnalytics
```

Description

```text
Athena Workgroup for AI Supply Chain Control Tower
```

State

```text
Enabled
```

---

# Bước 4. Thiết lập Query Result

Output location

```text
s3://ai-supplychain-datalake/athena-results/
```

Bật

```text
Override client-side settings
```

---

# Bước 5. Bật Encryption

Encryption

```text
SSE-S3
```

Hoặc

```text
SSE-KMS
```

Khuyến nghị Production:

```text
SSE-KMS
```

---

# Bước 6. Bật CloudWatch Metrics

Enable

```text
Publish query metrics
```

CloudWatch sẽ theo dõi:

```text
Query Duration

Query Count

Bytes Scanned

Failed Queries
```

---

# Bước 7. Chọn Workgroup

Quay lại Query Editor.

Đổi Workgroup:

```text
SupplyChainAnalytics
```

---

# Bước 8. Chạy thử truy vấn

```sql
SELECT COUNT(*)
FROM orders_partitioned;
```

Kiểm tra:

```text
Query completed successfully
```

---

# Bước 9. Kiểm tra Query History

Chọn

```text
Recent Queries
```

Kiểm tra:

```text
Execution Time

Data Scanned

Query State
```

---

# Best Practices

- Mỗi môi trường nên có Workgroup riêng.
- Luôn bật Encryption.
- Dùng Output Location riêng.
- Theo dõi Query History.
- Theo dõi Bytes Scanned.

---

## Kiểm tra kết quả

Đảm bảo:

- Workgroup được tạo.
- Output Location đúng.
- Encryption bật.
- Truy vấn chạy thành công.
- Query History hiển thị.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Tạo Amazon Athena Workgroup.
- Thiết lập vị trí lưu kết quả truy vấn.
- Bật mã hóa.
- Chuẩn bị môi trường phân tích dữ liệu.
