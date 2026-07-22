---
title: "5.3.2 Tạo Public và Private Subnets"
date: 2024-01-01
weight: 2
chapter: false
---

# 5.3.2 Tạo Public và Private Subnets

## Tổng quan

Sau khi tạo Amazon VPC, bước tiếp theo là chia mạng thành các **Public Subnet** và **Private Subnet**.

Trong hệ thống **AI Supply Chain Control Tower**, việc phân chia Subnet giúp:

- Tăng tính bảo mật.
- Tách biệt các tài nguyên truy cập Internet với các tài nguyên nội bộ.
- Đảm bảo Amazon RDS chỉ hoạt động trong mạng riêng (Private Subnet).

---

## Mục tiêu

Sau bài này bạn sẽ:

- Hiểu Public và Private Subnet.
- Tạo 2 Public Subnets.
- Tạo 2 Private Subnets.
- Phân bố Subnets trên nhiều Availability Zones.

---

## Kiến trúc

```
VPC (10.0.0.0/16)

├── Public Subnet A
│      10.0.1.0/24
│
├── Public Subnet B
│      10.0.2.0/24
│
├── Private Subnet A
│      10.0.11.0/24
│
└── Private Subnet B
       10.0.12.0/24
```

---

## Tại sao cần nhiều Availability Zones?

AWS khuyến nghị triển khai tài nguyên trên nhiều AZ nhằm:

- Tăng khả năng chịu lỗi.
- Hỗ trợ High Availability.
- Đáp ứng kiến trúc Well-Architected Framework.

---

## Các bước thực hiện

### Bước 1. Mở VPC Dashboard

Đi đến:

```
VPC
→ Subnets
```

Nhấn:

```
Create subnet
```

---

### Bước 2. Chọn VPC

Chọn:

```
AI-SupplyChain-VPC
```

---

### Bước 3. Tạo Public Subnet A

| Thuộc tính | Giá trị |
|------------|----------|
| Name | Public-Subnet-A |
| Availability Zone | ap-southeast-1a |
| CIDR | 10.0.1.0/24 |

---

### Bước 4. Tạo Public Subnet B

| Thuộc tính | Giá trị |
|------------|----------|
| Name | Public-Subnet-B |
| Availability Zone | ap-southeast-1b |
| CIDR | 10.0.2.0/24 |

---

### Bước 5. Tạo Private Subnet A

| Thuộc tính | Giá trị |
|------------|----------|
| Name | Private-Subnet-A |
| Availability Zone | ap-southeast-1a |
| CIDR | 10.0.11.0/24 |

---

### Bước 6. Tạo Private Subnet B

| Thuộc tính | Giá trị |
|------------|----------|
| Name | Private-Subnet-B |
| Availability Zone | ap-southeast-1b |
| CIDR | 10.0.12.0/24 |

---

### Bước 7. Kiểm tra kết quả

Danh sách Subnets sẽ hiển thị:

| Name | CIDR | AZ |
|------|------|----|
| Public-Subnet-A | 10.0.1.0/24 | ap-southeast-1a |
| Public-Subnet-B | 10.0.2.0/24 | ap-southeast-1b |
| Private-Subnet-A | 10.0.11.0/24 | ap-southeast-1a |
| Private-Subnet-B | 10.0.12.0/24 | ap-southeast-1b |

> Chèn hình: Subnet List

---

## Best Practices

- Public Subnet chỉ dành cho các tài nguyên cần Internet.
- Không triển khai Database trong Public Subnet.
- Luôn tạo ít nhất hai Private Subnets ở hai Availability Zones.

---

## Kết quả

Sau bài này bạn đã:

- Tạo thành công 4 Subnets.
- Phân chia Public và Private Network.
- Chuẩn bị hạ tầng cho Amazon RDS và Backend.