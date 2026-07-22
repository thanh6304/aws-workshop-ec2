---
title: "5.6.7 Xây dựng các truy vấn phân tích với Amazon Athena"
date: 2024-01-01
weight: 7
chapter: false
---

# 5.6.7 Xây dựng các truy vấn phân tích với Amazon Athena

## Tổng quan

Sau khi dữ liệu đã được lưu trong Amazon S3, đăng ký trong AWS Glue Data Catalog và tối ưu bằng Apache Parquet cùng Partitioning, bước tiếp theo là khai thác dữ liệu bằng Amazon Athena.

Trong bài này, chúng ta sẽ xây dựng các truy vấn SQL phục vụ phân tích hoạt động của hệ thống AI Supply Chain Control Tower.

---

## Kiến trúc

```text
Amazon S3
      │
      ▼
Glue Data Catalog
      │
      ▼
Amazon Athena
      │
      ▼
Business Analytics
      │
      ├── Inventory
      ├── Orders
      ├── Shipments
      └── Suppliers
```

---

## Mục tiêu

Sau khi hoàn thành bài này, bạn sẽ:

- Truy vấn dữ liệu bằng SQL.
- Phân tích tồn kho.
- Phân tích doanh thu.
- Theo dõi đơn hàng.
- Đánh giá vận chuyển.
- Đánh giá nhà cung cấp.
- Chuẩn bị dữ liệu cho Dashboard và AI.

---

# Inventory Analytics

## Truy vấn 1. Tổng số sản phẩm

```sql
SELECT COUNT(*) AS total_products
FROM inventory_partitioned;
```

---

## Truy vấn 2. Tổng tồn kho

```sql
SELECT
SUM(quantity) AS total_inventory
FROM inventory_partitioned;
```

---

## Truy vấn 3. Sản phẩm tồn kho thấp

```sql
SELECT
warehouse_id,
product_name,
quantity,
reorder_level
FROM inventory_partitioned
WHERE quantity < reorder_level
ORDER BY quantity;
```

---

## Truy vấn 4. Tồn kho theo kho

```sql
SELECT
warehouse_id,
SUM(quantity) total_stock
FROM inventory_partitioned
GROUP BY warehouse_id
ORDER BY total_stock DESC;
```

---

# Order Analytics

## Truy vấn 5. Tổng số đơn hàng

```sql
SELECT COUNT(*)
FROM orders_partitioned;
```

---

## Truy vấn 6. Doanh thu

```sql
SELECT
SUM(quantity*unit_price) revenue
FROM orders_partitioned
WHERE order_status<>'CANCELLED';
```

---

## Truy vấn 7. Doanh thu theo kho

```sql
SELECT
warehouse_id,
SUM(quantity*unit_price) revenue
FROM orders_partitioned
WHERE order_status<>'CANCELLED'
GROUP BY warehouse_id;
```

---

## Truy vấn 8. Đơn hàng theo trạng thái

```sql
SELECT
order_status,
COUNT(*) total
FROM orders_partitioned
GROUP BY order_status;
```

---

## Truy vấn 9. Top 5 sản phẩm bán chạy

```sql
SELECT
product_id,
SUM(quantity) total_sold
FROM orders_partitioned
GROUP BY product_id
ORDER BY total_sold DESC
LIMIT 5;
```

---

# Shipment Analytics

## Truy vấn 10. Số đơn giao thành công

```sql
SELECT COUNT(*)
FROM shipments_partitioned
WHERE shipment_status='DELIVERED';
```

---

## Truy vấn 11. Các đơn giao trễ

```sql
SELECT
shipment_id,
order_id,
carrier,
destination
FROM shipments_partitioned
WHERE shipment_status='DELAYED';
```

---

## Truy vấn 12. Thống kê theo Carrier

```sql
SELECT
carrier,
COUNT(*) total_shipments
FROM shipments_partitioned
GROUP BY carrier
ORDER BY total_shipments DESC;
```

---

# Supplier Analytics

## Truy vấn 13. Nhà cung cấp có tỷ lệ giao hàng đúng hạn cao nhất

```sql
SELECT
supplier_name,
on_time_delivery_rate
FROM suppliers_parquet
ORDER BY on_time_delivery_rate DESC
LIMIT 5;
```

---

## Truy vấn 14. Nhà cung cấp cần đánh giá

```sql
SELECT
supplier_name,
quality_score,
status
FROM suppliers_parquet
WHERE status='REVIEW';
```

---

## Truy vấn 15. Lead Time trung bình

```sql
SELECT
AVG(lead_time_days)
FROM suppliers_parquet;
```

---

# Business KPI

## Truy vấn 16. Giá trị tồn kho

```sql
SELECT
SUM(quantity*unit_cost)
AS inventory_value
FROM inventory_partitioned;
```

---

## Truy vấn 17. Doanh thu theo ngày

```sql
SELECT
order_date,
SUM(quantity*unit_price)
AS revenue
FROM orders_partitioned
GROUP BY order_date
ORDER BY order_date;
```

---

## Truy vấn 18. Kho có nhiều đơn hàng nhất

```sql
SELECT
warehouse_id,
COUNT(*) total_orders
FROM orders_partitioned
GROUP BY warehouse_id
ORDER BY total_orders DESC;
```

---

## Truy vấn 19. Tỷ lệ giao thành công

```sql
SELECT
ROUND(
100.0*
SUM(
CASE
WHEN shipment_status='DELIVERED'
THEN 1
ELSE 0
END
)/COUNT(*),
2)
AS delivery_rate;
```

---

## Truy vấn 20. Tổng quan Dashboard

```sql
SELECT
(SELECT COUNT(*) FROM orders_partitioned) total_orders,
(SELECT COUNT(*) FROM inventory_partitioned) total_inventory_items,
(SELECT COUNT(*) FROM shipments_partitioned) total_shipments,
(SELECT COUNT(*) FROM suppliers_parquet) total_suppliers;
```

---

## Best Practices

- Luôn sử dụng Partition Filter khi có thể.
- Chỉ chọn các cột cần thiết.
- Tránh sử dụng `SELECT *` trên bảng lớn.
- Theo dõi chỉ số **Data Scanned** sau mỗi truy vấn.
- Ưu tiên truy vấn trên bảng Parquet thay vì CSV.

---

## Kiểm tra kết quả

Đảm bảo:

- Tất cả truy vấn thực thi thành công.
- Kết quả trả về đúng với dữ liệu mẫu.
- Không có lỗi cú pháp SQL.
- Có thể sử dụng kết quả để xây dựng Dashboard.

---

## Kết quả

Sau khi hoàn thành bài này, bạn đã:

- Xây dựng các truy vấn phân tích trên Amazon Athena.
- Phân tích dữ liệu tồn kho, đơn hàng, vận chuyển và nhà cung cấp.
- Tính toán các KPI quan trọng của chuỗi cung ứng.
- Chuẩn bị dữ liệu cho Dashboard và AI Analysis.
