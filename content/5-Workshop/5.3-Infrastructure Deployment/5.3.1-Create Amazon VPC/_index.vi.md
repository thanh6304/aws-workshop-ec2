---
title: "5.3.1 Tạo Amazon VPC"
date: 2024-01-01
weight: 1
chapter: false
---


## Tổng quan

Amazon Virtual Private Cloud (Amazon VPC) là dịch vụ cho phép bạn tạo một mạng riêng biệt trên AWS để triển khai các tài nguyên như EC2, RDS, Lambda (thông qua VPC), và nhiều dịch vụ khác.

Trong hệ thống **AI Supply Chain Control Tower**, Amazon VPC đóng vai trò là lớp mạng trung tâm giúp:

- Cô lập hệ thống với Internet.
- Quản lý luồng giao tiếp giữa các dịch vụ.
- Tăng cường tính bảo mật.
- Hỗ trợ triển khai cơ sở dữ liệu trong Private Subnet.

Sau khi hoàn thành bước này, toàn bộ hạ tầng sẽ được xây dựng bên trong VPC.

---

## Mục tiêu

Trong bài này bạn sẽ:

- Tạo một Amazon VPC.
- Cấu hình IPv4 CIDR Block.
- Bật DNS Resolution.
- Bật DNS Hostnames.
- Kiểm tra VPC sau khi tạo.

---

## Kiến trúc

```
Internet
    │
Amazon VPC
    │
 ┌─────────────┐
 │ Public      │
 │ Private     │
 └─────────────┘
```

---

## Các bước thực hiện

### Bước 1. Mở VPC Console

Đăng nhập AWS Management Console.

Tìm kiếm dịch vụ:

```
VPC
```

Chọn **VPC Dashboard**.

<center>

![Kiến trúc Amazon VPC](/images/5-Workshop/5.3-S3-vpc/5.3.1.png)

</center>

<p align="center">
  <b>Hình 5.3.1.</b> Kiến trúc Amazon VPC của hệ thống AI Supply Chain Control Tower.
</p>

---

### Bước 2. Tạo VPC

Chọn:

```
Create VPC
```

---

### Bước 3. Cấu hình VPC

Nhập các thông tin sau:

| Thuộc tính          | Giá trị            |
| ------------------- | ------------------ |
| Resources to create | VPC only           |
| Name tag            | AI-SupplyChain-VPC |
| IPv4 CIDR           | 10.0.0.0/16        |
| IPv6 CIDR           | No IPv6            |
| Tenancy             | Default            |

> Chèn hình: Create VPC

---

### Bước 4. Tạo VPC

Nhấn:

```
Create VPC
```

AWS sẽ tự động tạo Virtual Private Cloud.

---

### Bước 5. Kiểm tra DNS

Sau khi tạo thành công:

Chọn VPC vừa tạo.

Kiểm tra:

- DNS Resolution = Enabled
- DNS Hostnames = Enabled

Nếu chưa bật:

```
Actions
Edit VPC Settings
```

Bật cả hai tùy chọn.

> Chèn hình: Edit DNS Settings

---

### Bước 6. Kiểm tra kết quả

Danh sách VPC sẽ xuất hiện:

| Name               | CIDR        |
| ------------------ | ----------- |
| AI-SupplyChain-VPC | 10.0.0.0/16 |

---

## Kết quả

Sau khi hoàn thành bài này bạn đã:

- Tạo thành công Amazon VPC.
- Thiết lập dải địa chỉ IPv4.
- Kích hoạt DNS Resolution.
- Kích hoạt DNS Hostnames.

VPC này sẽ được sử dụng ở các bài tiếp theo để triển khai Subnet, Security Group và Amazon RDS.
