---
title: "5.5.6 Lưu kết quả phân tích AI"
date: 2024-01-01
weight: 6
chapter: false
---


## Tổng quan

Sau khi AI Worker Lambda hoàn thành việc truy vấn dữ liệu bằng Amazon Athena và nhận kết quả phân tích từ Amazon Bedrock, bước tiếp theo là lưu kết quả vào Amazon RDS PostgreSQL.

Việc lưu trữ kết quả giúp hệ thống:

- Theo dõi trạng thái xử lý của từng yêu cầu AI.
- Hiển thị lịch sử phân tích trên Dashboard.
- Cho phép người dùng xem lại các báo cáo trước đó.
- Hỗ trợ kiểm toán và phân tích dữ liệu trong tương lai.

Thay vì chỉ trả về kết quả trực tiếp cho người dùng, hệ thống sẽ lưu toàn bộ thông tin của mỗi lần phân tích vào cơ sở dữ liệu.

---

# Kiến trúc

```
Amazon Bedrock
        │
        ▼
AI Worker Lambda
        │
        ▼
Amazon RDS PostgreSQL
        │
        ▼
Dashboard / Backend API
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo bảng lưu kết quả AI.
- Thiết kế cấu trúc dữ liệu.
- Lưu kết quả từ Lambda Worker.
- Cập nhật trạng thái xử lý.
- Kiểm tra dữ liệu trong PostgreSQL.

---

# Thiết kế bảng dữ liệu

Tạo bảng:

```sql
CREATE TABLE ai_analysis_results (
    id BIGSERIAL PRIMARY KEY,
    request_id VARCHAR(100) UNIQUE NOT NULL,
    warehouse VARCHAR(50),
    request_type VARCHAR(50),
    status VARCHAR(20),
    summary TEXT,
    recommendation TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP
);
```

Ý nghĩa các cột:

| Cột            | Mô tả                |
| -------------- | -------------------- |
| request_id     | Mã yêu cầu AI        |
| warehouse      | Kho được phân tích   |
| request_type   | Loại phân tích       |
| status         | Trạng thái xử lý     |
| summary        | Nội dung phân tích   |
| recommendation | Khuyến nghị từ AI    |
| created_at     | Thời gian tạo        |
| completed_at   | Thời gian hoàn thành |

---

# Trạng thái xử lý

Mỗi yêu cầu AI sẽ trải qua các trạng thái sau:

| Status     | Ý nghĩa           |
| ---------- | ----------------- |
| PENDING    | Đã gửi vào Queue  |
| PROCESSING | Lambda đang xử lý |
| COMPLETED  | Hoàn thành        |
| FAILED     | Xử lý thất bại    |

Luồng trạng thái:

```
PENDING
     │
     ▼
PROCESSING
     │
 ┌───┴────┐
 ▼        ▼
COMPLETED FAILED
```

---

# Các bước thực hiện

## Bước 1. Lưu yêu cầu ban đầu

Ngay sau khi Backend Lambda gửi Message vào Amazon SQS, tạo một bản ghi:

```text
Status

↓

PENDING
```

Ví dụ:

```sql
INSERT INTO ai_analysis_results(
    request_id,
    warehouse,
    request_type,
    status
)
VALUES(
    'REQ-001',
    'WH001',
    'DEMAND_FORECAST',
    'PENDING'
);
```

---

## Bước 2. Cập nhật trạng thái Processing

Khi AI Worker Lambda bắt đầu xử lý:

```sql
UPDATE ai_analysis_results
SET status='PROCESSING'
WHERE request_id='REQ-001';
```

---

## Bước 3. Lưu kết quả AI

Sau khi Amazon Bedrock trả về kết quả:

```text
Summary

Recommendation
```

Lambda cập nhật:

```sql
UPDATE ai_analysis_results
SET
status='COMPLETED',
summary='Inventory level is below safety stock.',
recommendation='Increase inventory for Laptop Dell.',
completed_at=NOW()
WHERE request_id='REQ-001';
```

---

## Bước 4. Xử lý lỗi

Nếu xảy ra lỗi:

```sql
UPDATE ai_analysis_results
SET status='FAILED'
WHERE request_id='REQ-001';
```

Điều này giúp quản trị viên dễ dàng xác định các yêu cầu cần xử lý lại.

---

# Ví dụ dữ liệu

| Request ID | Warehouse | Status     | Summary                |
| ---------- | --------- | ---------- | ---------------------- |
| REQ-001    | WH001     | COMPLETED  | Low inventory detected |
| REQ-002    | WH002     | PROCESSING | -                      |
| REQ-003    | WH003     | FAILED     | -                      |

---

# Luồng xử lý

```
Amazon SQS
      │
      ▼
AI Worker Lambda
      │
      ▼
Amazon Athena
      │
      ▼
Amazon Bedrock
      │
      ▼
Amazon RDS PostgreSQL
      │
      ▼
Dashboard
```

---

# Kiểm tra dữ liệu

Mở PostgreSQL và thực hiện:

```sql
SELECT *
FROM ai_analysis_results
ORDER BY created_at DESC;
```

Ví dụ:

| Request ID | Status     | Recommendation     |
| ---------- | ---------- | ------------------ |
| REQ-001    | COMPLETED  | Increase inventory |
| REQ-002    | PROCESSING | -                  |

---

# Best Practices

AWS khuyến nghị:

- Lưu Request ID duy nhất cho mỗi yêu cầu.
- Không ghi Prompt đầy đủ nếu chứa dữ liệu nhạy cảm.
- Chỉ lưu kết quả AI cần thiết.
- Ghi thời gian bắt đầu và hoàn thành để theo dõi hiệu năng.
- Đánh index cho `request_id` và `status` để tăng tốc truy vấn.

---

# Kiểm tra kết quả

Đảm bảo:

- Bản ghi được tạo khi yêu cầu được gửi.
- Trạng thái được cập nhật chính xác.
- Kết quả AI được lưu đầy đủ.
- Dashboard có thể đọc dữ liệu từ PostgreSQL.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Thiết kế bảng lưu kết quả AI.
- Lưu kết quả phân tích vào Amazon RDS PostgreSQL.
- Theo dõi trạng thái xử lý của từng yêu cầu.
- Chuẩn bị dữ liệu cho Dashboard và các API báo cáo.
