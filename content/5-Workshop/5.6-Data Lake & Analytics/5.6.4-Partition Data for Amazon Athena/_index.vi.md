---
title: "5.6.4 Phân vùng dữ liệu cho Amazon Athena"
date: 2024-01-01
weight: 4
chapter: false
---

# 5.6.4 Phân vùng dữ liệu cho Amazon Athena

## Tổng quan

Sau khi chuyển dữ liệu từ CSV sang Apache Parquet, bước tiếp theo là tổ chức dữ liệu thành các phân vùng.

Phân vùng chia dữ liệu thành các thư mục riêng biệt trong Amazon S3 dựa trên một hoặc nhiều trường, chẳng hạn như:

```text
year
month
day
warehouse_id
```

Khi truy vấn có điều kiện lọc theo Partition Key, Amazon Athena chỉ cần đọc các phân vùng phù hợp thay vì quét toàn bộ tập dữ liệu.

Ví dụ, truy vấn:

```sql
WHERE year = '2026'
  AND month = '07'
```

sẽ chỉ đọc dữ liệu trong phân vùng tương ứng.

Trong bài này, chúng ta sẽ sử dụng Athena CTAS để tạo các bảng Parquet có phân vùng.

---

## Kiến trúc

```text
Amazon S3 Raw Zone
      │
      │ CSV
      ▼
Amazon Athena CTAS
      │
      │ Parquet + Partition
      ▼
Amazon S3 Processed Zone
      │
      ├── inventory-partitioned/
      │   └── warehouse_id=WH001/
      │       └── year=2026/
      │           └── month=07/
      │
      ├── orders-partitioned/
      │   └── year=2026/
      │       └── month=07/
      │
      └── shipments-partitioned/
          └── year=2026/
              └── month=07/
                     │
                     ▼
          AWS Glue Data Catalog
                     │
                     ▼
               Amazon Athena
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Hiểu cách phân vùng dữ liệu trong Amazon S3.
- Chọn Partition Key phù hợp với từng tập dữ liệu.
- Tạo bảng Parquet có phân vùng bằng Athena CTAS.
- Kiểm tra cấu trúc thư mục phân vùng trong Amazon S3.
- Truy vấn dữ liệu bằng Partition Filter.
- So sánh lượng dữ liệu được quét.
- Hiểu cách cập nhật metadata phân vùng.

---

## Cấu trúc phân vùng đề xuất

### Inventory

Dữ liệu tồn kho thường được truy vấn theo kho và thời gian cập nhật.

```text
warehouse_id
year
month
```

Cấu trúc Amazon S3:

```text
processed/inventory-partitioned/
└── warehouse_id=WH001/
    └── year=2026/
        └── month=07/
            └── data.parquet
```

### Orders

Dữ liệu đơn hàng thường được phân tích theo thời gian.

```text
year
month
```

Cấu trúc:

```text
processed/orders-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

### Shipments

Dữ liệu vận chuyển thường được truy vấn theo ngày giao dự kiến hoặc trạng thái vận chuyển.

Trong workshop này, chúng ta sử dụng:

```text
year
month
```

Cấu trúc:

```text
processed/shipments-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

### Suppliers

Dữ liệu nhà cung cấp có kích thước nhỏ và thay đổi không thường xuyên. Vì vậy, trong workshop này, bảng `suppliers_parquet` sẽ không được phân vùng.

---

## Lưu ý trước khi thực hiện

Athena CTAS yêu cầu vị trí `external_location` chưa chứa dữ liệu.

Không sử dụng lại trực tiếp các thư mục:

```text
processed/inventory/
processed/orders/
processed/shipments/
```

Thay vào đó, chúng ta tạo các thư mục mới:

```text
processed/inventory-partitioned/
processed/orders-partitioned/
processed/shipments-partitioned/
```

Nếu đã chạy CTAS trước đó, hãy xóa bảng thử nghiệm và dữ liệu tại đường dẫn đích trước khi chạy lại.

---

# Bước 1. Mở Amazon Athena

Đăng nhập AWS Management Console.

Tìm kiếm và mở:

```text
Amazon Athena
```

Trong Query Editor, chọn:

```text
Data source: AwsDataCatalog
```

Chọn Database:

```text
supplychain_catalog
```

---

# Bước 2. Kiểm tra kiểu dữ liệu ngày

Chạy câu lệnh:

```sql
DESCRIBE inventory;
```

Tiếp tục kiểm tra:

```sql
DESCRIBE orders;
```

```sql
DESCRIBE shipments;
```

Nếu các cột ngày được Glue Crawler nhận diện là `string`, chúng ta có thể lấy năm và tháng bằng hàm `substr`.

Ví dụ:

```sql
substr(order_date, 1, 4)
substr(order_date, 6, 2)
```

Nếu cột đã có kiểu `date`, có thể sử dụng:

```sql
CAST(year(order_date) AS varchar)
CAST(month(order_date) AS varchar)
```

Các câu lệnh trong workshop giả định cột ngày hiện đang có kiểu:

```text
string
```

---

# Bước 3. Tạo bảng Inventory có phân vùng

Chạy câu lệnh:

```sql
CREATE TABLE inventory_partitioned
WITH (
    format = 'PARQUET',
    parquet_compression = 'SNAPPY',
    external_location = 's3://ai-supplychain-datalake/processed/inventory-partitioned/',
    partitioned_by = ARRAY['warehouse_id', 'year', 'month']
)
AS
SELECT
    inventory_id,
    product_id,
    product_name,
    quantity,
    reorder_level,
    unit_cost,
    last_updated,
    warehouse_id,
    substr(last_updated, 1, 4) AS year,
    substr(last_updated, 6, 2) AS month
FROM inventory;
```

Các cột phân vùng phải được đặt ở cuối danh sách `SELECT` của câu lệnh CTAS.

Kết quả được lưu theo cấu trúc:

```text
processed/inventory-partitioned/
├── warehouse_id=WH001/
│   └── year=2026/
│       └── month=07/
│
├── warehouse_id=WH002/
│   └── year=2026/
│       └── month=07/
│
└── warehouse_id=WH003/
    └── year=2026/
        └── month=07/
```

---

# Bước 4. Kiểm tra bảng Inventory

Chạy:

```sql
SELECT *
FROM inventory_partitioned
LIMIT 10;
```

Lọc theo kho:

```sql
SELECT
    product_id,
    product_name,
    quantity,
    reorder_level
FROM inventory_partitioned
WHERE warehouse_id = 'WH001'
  AND year = '2026'
  AND month = '07';
```

Tìm sản phẩm tồn kho thấp:

```sql
SELECT
    warehouse_id,
    product_id,
    product_name,
    quantity,
    reorder_level
FROM inventory_partitioned
WHERE year = '2026'
  AND month = '07'
  AND quantity < reorder_level
ORDER BY warehouse_id, quantity;
```

---

# Bước 5. Tạo bảng Orders có phân vùng

Chạy:

```sql
CREATE TABLE orders_partitioned
WITH (
    format = 'PARQUET',
    parquet_compression = 'SNAPPY',
    external_location = 's3://ai-supplychain-datalake/processed/orders-partitioned/',
    partitioned_by = ARRAY['year', 'month']
)
AS
SELECT
    order_id,
    customer_id,
    product_id,
    warehouse_id,
    quantity,
    unit_price,
    order_status,
    order_date,
    substr(order_date, 1, 4) AS year,
    substr(order_date, 6, 2) AS month
FROM orders;
```

Cấu trúc kết quả:

```text
processed/orders-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

---

# Bước 6. Kiểm tra bảng Orders

Chạy:

```sql
SELECT *
FROM orders_partitioned
LIMIT 10;
```

Truy vấn đơn hàng trong tháng 7 năm 2026:

```sql
SELECT
    order_id,
    customer_id,
    order_status,
    quantity,
    unit_price,
    order_date
FROM orders_partitioned
WHERE year = '2026'
  AND month = '07'
ORDER BY order_date;
```

Tính doanh thu theo kho:

```sql
SELECT
    warehouse_id,
    SUM(quantity * unit_price) AS total_revenue
FROM orders_partitioned
WHERE year = '2026'
  AND month = '07'
  AND order_status <> 'CANCELLED'
GROUP BY warehouse_id
ORDER BY total_revenue DESC;
```

---

# Bước 7. Tạo bảng Shipments có phân vùng

Chạy:

```sql
CREATE TABLE shipments_partitioned
WITH (
    format = 'PARQUET',
    parquet_compression = 'SNAPPY',
    external_location = 's3://ai-supplychain-datalake/processed/shipments-partitioned/',
    partitioned_by = ARRAY['year', 'month']
)
AS
SELECT
    shipment_id,
    order_id,
    carrier,
    origin_warehouse,
    destination,
    shipment_status,
    expected_date,
    actual_date,
    substr(expected_date, 1, 4) AS year,
    substr(expected_date, 6, 2) AS month
FROM shipments;
```

Cấu trúc kết quả:

```text
processed/shipments-partitioned/
└── year=2026/
    └── month=07/
        └── data.parquet
```

---

# Bước 8. Kiểm tra bảng Shipments

Truy vấn dữ liệu:

```sql
SELECT *
FROM shipments_partitioned
LIMIT 10;
```

Tìm các lô hàng đang trễ:

```sql
SELECT
    shipment_id,
    order_id,
    carrier,
    destination,
    expected_date,
    actual_date,
    shipment_status
FROM shipments_partitioned
WHERE year = '2026'
  AND month = '07'
  AND (
      shipment_status = 'DELAYED'
      OR (
          actual_date IS NOT NULL
          AND actual_date <> ''
          AND CAST(actual_date AS date) > CAST(expected_date AS date)
      )
  )
ORDER BY expected_date;
```

---

# Bước 9. Kiểm tra cấu trúc trên Amazon S3

Mở:

```text
Amazon S3
```

Chọn Bucket:

```text
ai-supplychain-datalake
```

Mở:

```text
processed/
```

Kết quả dự kiến:

```text
processed/
│
├── inventory/
├── inventory-partitioned/
│   ├── warehouse_id=WH001/
│   ├── warehouse_id=WH002/
│   └── warehouse_id=WH003/
│
├── orders/
├── orders-partitioned/
│   └── year=2026/
│
├── shipments/
├── shipments-partitioned/
│   └── year=2026/
│
└── suppliers/
```

Athena CTAS tự động ghi dữ liệu vào các thư mục phân vùng và thêm metadata của các phân vùng vào bảng mới.

---

# Bước 10. Kiểm tra danh sách Partition

Trong Athena Query Editor, chạy:

```sql
SHOW PARTITIONS inventory_partitioned;
```

Kết quả dự kiến:

```text
warehouse_id=WH001/year=2026/month=07
warehouse_id=WH002/year=2026/month=07
warehouse_id=WH003/year=2026/month=07
```

Kiểm tra Orders:

```sql
SHOW PARTITIONS orders_partitioned;
```

Kết quả:

```text
year=2026/month=07
```

Kiểm tra Shipments:

```sql
SHOW PARTITIONS shipments_partitioned;
```

---

# Bước 11. So sánh truy vấn có và không có Partition Filter

## Không sử dụng Partition Filter

```sql
SELECT
    warehouse_id,
    SUM(quantity * unit_price) AS total_revenue
FROM orders_partitioned
GROUP BY warehouse_id;
```

## Có sử dụng Partition Filter

```sql
SELECT
    warehouse_id,
    SUM(quantity * unit_price) AS total_revenue
FROM orders_partitioned
WHERE year = '2026'
  AND month = '07'
GROUP BY warehouse_id;
```

Sau mỗi truy vấn, kiểm tra:

```text
Query statistics
Data scanned
Execution time
```

Với bộ dữ liệu mẫu nhỏ, sự khác biệt có thể chưa rõ ràng. Khi dữ liệu có nhiều tháng hoặc nhiều năm, Partition Filter sẽ giúp Athena bỏ qua các thư mục không liên quan.

---

# Bước 12. Thêm dữ liệu mới vào bảng phân vùng

Khi có dữ liệu của tháng mới, có thể sử dụng `INSERT INTO`.

Ví dụ:

```sql
INSERT INTO orders_partitioned
SELECT
    order_id,
    customer_id,
    product_id,
    warehouse_id,
    quantity,
    unit_price,
    order_status,
    order_date,
    substr(order_date, 1, 4) AS year,
    substr(order_date, 6, 2) AS month
FROM orders
WHERE substr(order_date, 1, 4) = '2026'
  AND substr(order_date, 6, 2) = '08';
```

Athena sẽ ghi dữ liệu vào phân vùng tương ứng:

```text
year=2026/month=08/
```

Không chạy lại câu lệnh này cho cùng một tập dữ liệu nếu chưa có cơ chế chống trùng lặp, vì dữ liệu có thể được ghi thêm lần nữa.

---

# Bước 13. Đăng ký Partition cho dữ liệu có sẵn

Nếu dữ liệu được một công cụ khác ghi trực tiếp vào Amazon S3 theo cấu trúc Hive-style:

```text
year=2026/month=08/
```

có thể cập nhật metadata bằng:

```sql
MSCK REPAIR TABLE orders_partitioned;
```

Hoặc thêm thủ công:

```sql
ALTER TABLE orders_partitioned
ADD IF NOT EXISTS
PARTITION (
    year = '2026',
    month = '08'
)
LOCATION 's3://ai-supplychain-datalake/processed/orders-partitioned/year=2026/month=08/';
```

Đối với bảng được tạo trực tiếp bằng CTAS, các phân vùng do CTAS tạo đã được đăng ký tự động.

---

## Xóa bảng và chạy lại CTAS

Nếu cần tạo lại bảng:

```sql
DROP TABLE IF EXISTS inventory_partitioned;
```

Lệnh `DROP TABLE` chỉ xóa metadata của bảng trong AWS Glue Data Catalog. Dữ liệu Parquet trong Amazon S3 vẫn có thể còn tồn tại.

Xóa dữ liệu cũ bằng AWS CLI:

```bash
aws s3 rm \
s3://ai-supplychain-datalake/processed/inventory-partitioned/ \
--recursive
```

Sau đó mới chạy lại câu lệnh CTAS.

Thực hiện tương tự với:

```text
orders-partitioned
shipments-partitioned
```

---

## Lựa chọn Partition Key

Nên chọn trường:

- Xuất hiện thường xuyên trong điều kiện `WHERE`.
- Có số lượng giá trị hợp lý.
- Có khả năng loại bỏ phần lớn dữ liệu không cần thiết.
- Phù hợp với cách người dùng phân tích dữ liệu.

Partition Key phù hợp:

```text
year
month
day
warehouse_id
region
```

Không nên phân vùng trực tiếp theo các trường có quá nhiều giá trị riêng biệt như:

```text
order_id
customer_id
shipment_id
```

Việc tạo quá nhiều phân vùng hoặc quá nhiều file nhỏ có thể làm tăng chi phí quản lý metadata và thời gian lập kế hoạch truy vấn.

---

## Lỗi thường gặp

| Lỗi                             | Nguyên nhân                           | Cách khắc phục                         |
| ------------------------------- | ------------------------------------- | -------------------------------------- |
| `HIVE_PATH_ALREADY_EXISTS`      | Thư mục đích đã có dữ liệu            | Xóa dữ liệu cũ hoặc dùng đường dẫn mới |
| Partition column not last       | Cột phân vùng không nằm cuối `SELECT` | Di chuyển Partition Column xuống cuối  |
| `TABLE_ALREADY_EXISTS`          | Bảng đã tồn tại                       | Đổi tên hoặc chạy `DROP TABLE`         |
| Không hiển thị Partition        | Metadata chưa được đăng ký            | Chạy `MSCK REPAIR TABLE`               |
| Truy vấn vẫn quét nhiều dữ liệu | Không lọc theo Partition Key          | Thêm điều kiện `WHERE year`, `month`   |
| Dữ liệu bị trùng                | Chạy `INSERT INTO` nhiều lần          | Kiểm tra dữ liệu trước khi ghi         |
| Sai năm hoặc tháng              | Ngày không đúng định dạng             | Chuẩn hóa ngày thành `YYYY-MM-DD`      |
| Quá nhiều Partition             | Chọn trường có độ phân biệt quá cao   | Thiết kế lại Partition Key             |

---

## Best Practices

- Kết hợp Partition với Apache Parquet.
- Sử dụng Partition Filter trong các truy vấn.
- Chọn Partition Key dựa trên mẫu truy vấn thực tế.
- Dùng định dạng thư mục Hive-style.
- Không phân vùng theo ID duy nhất.
- Tránh tạo quá nhiều file nhỏ.
- Không chạy lặp lại `INSERT INTO` nếu chưa kiểm tra dữ liệu.
- Sử dụng đường dẫn S3 riêng cho từng bảng.
- Kiểm tra `Data scanned` sau mỗi truy vấn.
- Sử dụng Partition Projection khi số lượng phân vùng tăng rất lớn.
- Giữ Partition Key ở kiểu `string` để Athena lọc phân vùng hiệu quả.

---

## Kiểm tra kết quả

Đảm bảo:

- Bảng `inventory_partitioned` đã được tạo.
- Bảng `orders_partitioned` đã được tạo.
- Bảng `shipments_partitioned` đã được tạo.
- Dữ liệu được lưu ở định dạng Parquet.
- Các thư mục phân vùng xuất hiện trong Amazon S3.
- Lệnh `SHOW PARTITIONS` trả về kết quả.
- Truy vấn có thể lọc theo `year`, `month` và `warehouse_id`.
- Không có dữ liệu được ghi nhầm vào Raw Zone.
- Dữ liệu nhà cung cấp vẫn sử dụng bảng `suppliers_parquet`.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Phân vùng dữ liệu tồn kho theo kho, năm và tháng.
- Phân vùng dữ liệu đơn hàng theo năm và tháng.
- Phân vùng dữ liệu vận chuyển theo năm và tháng.
- Kết hợp Apache Parquet với Partitioning.
- Kiểm tra metadata phân vùng trong Athena.
- Chuẩn bị dữ liệu tối ưu cho các truy vấn phân tích và AI.
