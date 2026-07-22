---
title: "5.3.3 Cấu hình Security Groups"
date: 2024-01-01
weight: 3
chapter: false
---


## Tổng quan

Sau khi hoàn thành việc tạo VPC và Subnets, bước tiếp theo là cấu hình **Amazon EC2 Security Groups**.

Security Group hoạt động như một **tường lửa (Firewall)** ở cấp độ tài nguyên AWS, giúp kiểm soát lưu lượng mạng được phép đi vào (Inbound) và đi ra (Outbound).

Trong hệ thống **AI Supply Chain Control Tower**, chúng ta sẽ sử dụng Security Groups để:

- Cho phép Lambda truy cập Amazon RDS.
- Ngăn chặn truy cập trái phép từ Internet.
- Bảo vệ cơ sở dữ liệu PostgreSQL.
- Tuân thủ nguyên tắc **Least Privilege** trong AWS Well-Architected Framework.

---

# Kiến trúc

```
                Internet
                    │
          ─────────────────────
                    │
               API Gateway
                    │
                Lambda API
                    │
          Lambda Security Group
                    │
           PostgreSQL Port 5432
                    │
             RDS Security Group
                    │
               Amazon RDS
```

---

# Mục tiêu

Sau bài này bạn sẽ:

- Tạo Security Group cho Lambda.
- Tạo Security Group cho Amazon RDS.
- Cấu hình Inbound Rules.
- Cấu hình Outbound Rules.
- Kiểm tra kết nối giữa Lambda và RDS.

---

# Tại sao cần Security Group?

Nếu không sử dụng Security Group đúng cách:

- Database có thể bị truy cập từ Internet.
- Hacker có thể dò cổng PostgreSQL.
- Backend không thể kết nối Database.
- Gia tăng rủi ro rò rỉ dữ liệu.

Do đó AWS khuyến nghị chỉ cho phép các tài nguyên cần thiết được phép giao tiếp với nhau.

---

# Các bước thực hiện

## Bước 1. Mở EC2 Console

Từ AWS Console tìm kiếm:

```
EC2
```

Chọn:

```
Network & Security
→ Security Groups
```

<center>

![Kiến trúc Security](/images/5-Workshop/5.3-S3-vpc/5.3.3.png)

</center>

<p align="center">
  <b>Hình 5.3.1.</b> Kiến trúc Amazon VPC của hệ thống AI Supply Chain Control Tower.
</p>

---

## Bước 2. Tạo Security Group cho Lambda

Nhấn:

```
Create security group
```

Điền thông tin:

| Thuộc tính  | Giá trị                   |
| ----------- | ------------------------- |
| Name        | Lambda-SG                 |
| Description | Security Group for Lambda |
| VPC         | AI-SupplyChain-VPC        |

Không cần thêm Inbound Rule.

Outbound Rule giữ mặc định.

Nhấn:

```
Create Security Group
```

> Chèn hình: Lambda Security Group

---

## Bước 3. Tạo Security Group cho Amazon RDS

Tiếp tục chọn:

```
Create Security Group
```

Điền:

| Thuộc tính  | Giá trị                       |
| ----------- | ----------------------------- |
| Name        | RDS-SG                        |
| Description | Security Group for PostgreSQL |
| VPC         | AI-SupplyChain-VPC            |

---

## Bước 4. Cấu hình Inbound Rule

Thêm Rule:

| Type       | Protocol | Port | Source    |
| ---------- | -------- | ---- | --------- |
| PostgreSQL | TCP      | 5432 | Lambda-SG |

Điều này đồng nghĩa:

✔ Chỉ các Lambda sử dụng Lambda-SG mới có quyền truy cập PostgreSQL.

> Chèn hình: PostgreSQL Rule

---

## Bước 5. Kiểm tra Outbound Rule

Đối với Security Group của Lambda:

Outbound Rule mặc định:

```
All Traffic
```

Điều này cho phép Lambda kết nối tới RDS và các dịch vụ AWS khác.

---

## Bước 6. Kiểm tra Security Groups

Danh sách sẽ hiển thị:

| Name      |
| --------- |
| Lambda-SG |
| RDS-SG    |

---

# Best Practices

AWS khuyến nghị:

✔ Không mở PostgreSQL (5432) cho Internet.

✔ Không sử dụng:

```
0.0.0.0/0
```

cho Database.

✔ Chỉ cho phép Security Group của Lambda truy cập.

✔ Tuân thủ nguyên tắc Least Privilege.

---

# Kết quả

Sau bài này bạn đã:

- Tạo Security Group cho Lambda.
- Tạo Security Group cho Amazon RDS.
- Cấu hình PostgreSQL chỉ cho phép Lambda truy cập.
- Hoàn thành lớp bảo mật mạng cho Backend.
