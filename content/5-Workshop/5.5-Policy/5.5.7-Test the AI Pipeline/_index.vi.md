---
title: "5.5.7 Kiểm thử AI Pipeline"
date: 2024-01-01
weight: 7
chapter: false
---

## Tổng quan

Sau khi hoàn thành việc triển khai AWS Glue Data Catalog, Amazon Athena, AI Worker Lambda, Amazon SQS và Amazon Bedrock, bước cuối cùng là kiểm thử toàn bộ AI Pipeline theo luồng end-to-end.

Trong bài này, người dùng sẽ gửi một yêu cầu phân tích từ Postman hoặc Frontend. Yêu cầu sẽ đi qua Amazon API Gateway, Backend Lambda, Amazon SQS, AI Worker Lambda, Amazon Athena, Amazon Bedrock và cuối cùng được lưu vào Amazon RDS PostgreSQL.

Việc kiểm thử giúp xác nhận rằng tất cả thành phần trong hệ thống đã được cấu hình đúng và có thể phối hợp với nhau.

---

# Kiến trúc kiểm thử

```text
User / Postman
      │
      ▼
Amazon Cognito
      │
      ▼
Amazon API Gateway
      │
      ▼
Backend Lambda
      │
      ▼
Amazon SQS
      │
      ▼
AI Worker Lambda
      │
      ├── Amazon Athena
      ├── Amazon Bedrock
      └── Amazon RDS PostgreSQL
      │
      ▼
Dashboard / Backend API
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Gửi yêu cầu AI qua API.
- Kiểm tra thông điệp trong Amazon SQS.
- Xác minh AI Worker Lambda được kích hoạt.
- Kiểm tra truy vấn Amazon Athena.
- Kiểm tra phản hồi từ Amazon Bedrock.
- Xác minh kết quả được lưu vào PostgreSQL.
- Theo dõi toàn bộ quá trình trong Amazon CloudWatch.

---

# Điều kiện tiên quyết

Đảm bảo các thành phần sau đã sẵn sàng:

- Amazon Cognito User Pool.
- Amazon API Gateway.
- Backend Lambda.
- Amazon SQS Queue.
- AI Worker Lambda.
- AWS Glue Data Catalog.
- Amazon Athena.
- Amazon Bedrock.
- Amazon RDS PostgreSQL.
- Amazon CloudWatch Logs.

---

# Các bước thực hiện

## Bước 1. Kiểm tra dữ liệu trong Amazon S3

Mở Amazon S3 Console.

Chọn Data Lake Bucket:

```text
ai-supplychain-datalake
```

Đảm bảo các thư mục chứa dữ liệu:

```text
inventory/
orders/
shipments/
reports/
```

Ví dụ file:

```text
inventory/inventory.csv
orders/orders.csv
shipments/shipments.csv
```

Dữ liệu phải khớp với cấu trúc bảng đã được tạo trong AWS Glue Data Catalog.

---

## Bước 2. Kiểm tra AWS Glue Data Catalog

Mở AWS Glue Console.

Chọn:

```text
Data Catalog
```

Sau đó chọn Database:

```text
supplychain_catalog
```

Đảm bảo có các bảng:

```text
inventory
orders
shipments
reports
```

Kiểm tra các cột và kiểu dữ liệu của từng bảng.

---

## Bước 3. Kiểm tra truy vấn Amazon Athena

Mở Amazon Athena Console.

Chọn:

```text
Data Source: AwsDataCatalog
Database: supplychain_catalog
```

Thực hiện truy vấn:

```sql
SELECT
    product_name,
    quantity
FROM inventory
WHERE quantity < 20
ORDER BY quantity ASC;
```

Kết quả mong đợi:

| product_name   | quantity |
| -------------- | -------: |
| Keyboard       |        5 |
| Laptop Dell    |        8 |
| Mouse Logitech |       12 |

Nếu truy vấn thực thi thành công, Athena đã sẵn sàng cung cấp dữ liệu cho AI Worker Lambda.

---

## Bước 4. Lấy Access Token từ Amazon Cognito

Đăng nhập bằng tài khoản đã tạo trong Amazon Cognito.

Sau khi đăng nhập thành công, lấy:

```text
Access Token
```

Trong Postman, chọn:

```text
Authorization
```

Loại:

```text
Bearer Token
```

Dán Access Token vào trường Token.

---

## Bước 5. Gửi yêu cầu phân tích AI

Thực hiện yêu cầu:

```http
POST /ai/predict
```

Header:

```http
Authorization: Bearer <AccessToken>
Content-Type: application/json
```

Body:

```json
{
  "requestType": "DEMAND_FORECAST",
  "warehouse": "WH001",
  "forecastDays": 30
}
```

Ví dụ URL:

```text
https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/ai/predict
```

Kết quả mong đợi:

```json
{
  "requestId": "REQ-20260722-001",
  "status": "PENDING",
  "message": "AI analysis request submitted successfully"
}
```

HTTP Status:

```text
202 Accepted
```

Mã trạng thái `202 Accepted` phù hợp vì yêu cầu AI được xử lý bất đồng bộ.

---

## Bước 6. Kiểm tra bản ghi PENDING trong PostgreSQL

Sau khi Backend Lambda nhận yêu cầu, hệ thống sẽ tạo một bản ghi ban đầu.

Thực hiện:

```sql
SELECT
    request_id,
    warehouse,
    request_type,
    status,
    created_at
FROM ai_analysis_results
ORDER BY created_at DESC;
```

Kết quả mong đợi:

| request_id       | warehouse | request_type    | status  |
| ---------------- | --------- | --------------- | ------- |
| REQ-20260722-001 | WH001     | DEMAND_FORECAST | PENDING |

---

## Bước 7. Kiểm tra Amazon SQS

Mở Amazon SQS Console.

Chọn Queue:

```text
ai-processing-queue
```

Theo dõi:

```text
Messages available
Messages in flight
```

Khi AI Worker Lambda bắt đầu xử lý, Message có thể chuyển từ:

```text
Messages available
```

sang:

```text
Messages in flight
```

Sau khi xử lý thành công, Message sẽ được xóa khỏi Queue.

---

## Bước 8. Kiểm tra AI Worker Lambda

Mở AWS Lambda Console.

Chọn Function:

```text
ai-supplychain-worker
```

Chọn:

```text
Monitor
```

Sau đó mở:

```text
View CloudWatch Logs
```

Log Group:

```text
/aws/lambda/ai-supplychain-worker
```

Kết quả mong đợi:

```text
Processing request: REQ-20260722-001

Request status updated to PROCESSING

Athena query started

Athena query completed successfully

Prompt generated

Amazon Bedrock invocation started

Amazon Bedrock invocation completed

AI analysis result saved to PostgreSQL

Request status updated to COMPLETED
```

---

## Bước 9. Kiểm tra trạng thái PROCESSING

Trong khi Lambda đang thực hiện truy vấn và gọi Bedrock, kiểm tra PostgreSQL:

```sql
SELECT
    request_id,
    status
FROM ai_analysis_results
WHERE request_id = 'REQ-20260722-001';
```

Trạng thái có thể là:

```text
PROCESSING
```

Do tác vụ có thể hoàn thành nhanh, trong một số trường hợp bạn sẽ thấy trực tiếp trạng thái `COMPLETED`.

---

## Bước 10. Kiểm tra kết quả từ Amazon Bedrock

AI Worker Lambda sẽ gửi dữ liệu truy vấn từ Athena đến Amazon Bedrock.

Ví dụ dữ liệu đầu vào:

```text
Warehouse: WH001

Low-stock products:
- Keyboard: 5 units
- Laptop Dell: 8 units
- Mouse Logitech: 12 units

Delayed shipments: 18
Forecast period: 30 days
```

Ví dụ phản hồi AI:

```text
Summary:
Warehouse WH001 has several products below the recommended safety stock level.

Recommendation:
Increase Laptop Dell and Keyboard inventory immediately.
Review supplier lead times and prioritize delayed shipments.
Monitor demand weekly during the next 30 days.
```

---

## Bước 11. Kiểm tra kết quả trong PostgreSQL

Thực hiện:

```sql
SELECT
    request_id,
    warehouse,
    request_type,
    status,
    summary,
    recommendation,
    created_at,
    completed_at
FROM ai_analysis_results
WHERE request_id = 'REQ-20260722-001';
```

Kết quả mong đợi:

| Trường         | Giá trị                                     |
| -------------- | ------------------------------------------- |
| request_id     | REQ-20260722-001                            |
| warehouse      | WH001                                       |
| request_type   | DEMAND_FORECAST                             |
| status         | COMPLETED                                   |
| summary        | Warehouse inventory is below safety stock   |
| recommendation | Increase stock and review delayed shipments |
| completed_at   | Có giá trị thời gian                        |

---

## Bước 12. Kiểm tra API lấy kết quả

Thực hiện:

```http
GET /ai/results/REQ-20260722-001
```

Header:

```http
Authorization: Bearer <AccessToken>
```

Kết quả mong đợi:

```json
{
  "requestId": "REQ-20260722-001",
  "warehouse": "WH001",
  "requestType": "DEMAND_FORECAST",
  "status": "COMPLETED",
  "summary": "Warehouse inventory is below the recommended safety stock level.",
  "recommendation": "Increase stock for Laptop Dell and Keyboard and review delayed shipments.",
  "createdAt": "2026-07-22T09:30:00Z",
  "completedAt": "2026-07-22T09:31:15Z"
}
```

---

## Bước 13. Kiểm tra API lịch sử phân tích

Thực hiện:

```http
GET /ai/results
```

Kết quả mong đợi:

```json
[
  {
    "requestId": "REQ-20260722-001",
    "warehouse": "WH001",
    "status": "COMPLETED",
    "summary": "Low inventory risk detected."
  },
  {
    "requestId": "REQ-20260722-002",
    "warehouse": "WH002",
    "status": "PROCESSING",
    "summary": null
  }
]
```

API này có thể được Frontend sử dụng để hiển thị lịch sử phân tích AI.

---

# Kiểm thử trường hợp lỗi

## Trường hợp 1. JWT không hợp lệ

Gửi yêu cầu với Token không hợp lệ.

Kết quả mong đợi:

```text
HTTP 401 Unauthorized
```

---

## Trường hợp 2. Dữ liệu đầu vào không hợp lệ

Body:

```json
{
  "warehouse": "",
  "forecastDays": -5
}
```

Kết quả mong đợi:

```json
{
  "status": 400,
  "message": "Invalid AI analysis request"
}
```

---

## Trường hợp 3. Athena truy vấn thất bại

Có thể thử bằng cách cấu hình sai Database hoặc Table trong môi trường kiểm thử.

Kết quả mong đợi:

- Lambda ghi lỗi vào CloudWatch.
- Trạng thái yêu cầu được cập nhật thành `FAILED`.
- Message được thử lại theo cấu hình SQS.
- Sau khi vượt quá giới hạn Retry, Message được chuyển đến DLQ.

---

## Trường hợp 4. Amazon Bedrock bị từ chối truy cập

Nếu IAM Role thiếu quyền:

```text
bedrock:InvokeModel
```

CloudWatch có thể hiển thị:

```text
AccessDeniedException
```

Cách xử lý:

- Kiểm tra IAM Role.
- Kiểm tra Model ID.
- Kiểm tra Foundation Model có khả dụng trong Region đang sử dụng hay không.

---

## Trường hợp 5. Không kết nối được PostgreSQL

CloudWatch có thể hiển thị:

```text
Connection timed out
```

hoặc:

```text
Connection refused
```

Cách xử lý:

- Kiểm tra Lambda VPC.
- Kiểm tra Private Subnet.
- Kiểm tra Security Group.
- Kiểm tra Secret.
- Kiểm tra trạng thái Amazon RDS.

---

# Bảng tổng hợp kiểm thử

| Thành phần       | Nội dung kiểm tra  | Kết quả mong đợi      |
| ---------------- | ------------------ | --------------------- |
| Amazon Cognito   | Access Token       | Token hợp lệ          |
| API Gateway      | POST `/ai/predict` | HTTP 202              |
| Backend Lambda   | Gửi Message        | Thành công            |
| Amazon SQS       | Nhận Message       | Message xuất hiện     |
| AI Worker Lambda | Xử lý yêu cầu      | Lambda được kích hoạt |
| Amazon Athena    | Truy vấn dữ liệu   | Query thành công      |
| Amazon Bedrock   | Sinh phân tích     | Có phản hồi AI        |
| Amazon RDS       | Lưu kết quả        | Status COMPLETED      |
| Backend API      | Lấy kết quả        | HTTP 200              |
| CloudWatch       | Ghi Log            | Có Log đầy đủ         |

---

# Xử lý lỗi thường gặp

| Lỗi                                 | Nguyên nhân                      | Cách khắc phục                       |
| ----------------------------------- | -------------------------------- | ------------------------------------ |
| HTTP 401                            | JWT hết hạn hoặc không hợp lệ    | Lấy Access Token mới                 |
| HTTP 500                            | Backend Lambda gặp lỗi           | Kiểm tra CloudWatch Logs             |
| Message không xuất hiện trong Queue | Sai Queue URL hoặc thiếu IAM     | Kiểm tra biến môi trường và IAM Role |
| Worker không được kích hoạt         | SQS Trigger bị Disable           | Bật lại Trigger                      |
| Athena Query Failed                 | Sai Database, Table hoặc S3 Path | Kiểm tra Glue Data Catalog           |
| Bedrock Access Denied               | Thiếu quyền InvokeModel          | Cập nhật IAM Policy                  |
| Database Connection Failed          | Sai VPC, SG hoặc Secret          | Kiểm tra cấu hình RDS                |
| Status luôn PROCESSING              | Worker bị lỗi trước khi cập nhật | Kiểm tra Lambda Logs                 |
| Message vào DLQ                     | Vượt quá Maximum receives        | Kiểm tra lỗi gốc và xử lý lại        |

---

# Best Practices

- Sử dụng `202 Accepted` cho yêu cầu xử lý bất đồng bộ.
- Ghi `requestId` vào tất cả Log để dễ theo dõi.
- Không ghi Access Token, mật khẩu hoặc Secret vào CloudWatch Logs.
- Thiết lập Alarm cho Lambda Errors và DLQ Messages.
- Kiểm tra kết quả AI trước khi sử dụng trong quyết định quan trọng.
- Sử dụng Structured Logging thay vì Log văn bản không có định dạng.
- Theo dõi chi phí truy vấn Athena và số lần gọi Amazon Bedrock.
- Thực hiện kiểm thử lại sau mỗi lần thay đổi Prompt hoặc Foundation Model.

---

# Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Kiểm thử thành công toàn bộ AI Pipeline.
- Xác minh Backend Lambda gửi yêu cầu đến Amazon SQS.
- Xác minh AI Worker Lambda truy vấn Amazon Athena.
- Tích hợp và nhận phản hồi từ Amazon Bedrock.
- Lưu kết quả AI vào Amazon RDS PostgreSQL.
- Xây dựng API để Frontend theo dõi kết quả phân tích.
- Hoàn thiện luồng AI end-to-end của hệ thống AI Supply Chain Control Tower.
