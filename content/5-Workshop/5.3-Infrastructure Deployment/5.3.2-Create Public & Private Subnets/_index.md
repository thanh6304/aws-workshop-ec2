---
title: "5.3.2 Create Public and Private Subnets"
date: 2024-01-01
weight: 2
chapter: false
---


## Overview

After creating the Amazon VPC, the next step is to divide the network into **Public** and **Private Subnets**.

For the **AI Supply Chain Control Tower**, separating workloads into different subnets improves security, scalability, and availability.

Private Subnets will host backend resources such as Amazon RDS, while Public Subnets will support internet-facing components when needed.

---

## Objectives

After completing this section, you will be able to:

- Understand the purpose of Public and Private Subnets.
- Create two Public Subnets.
- Create two Private Subnets.
- Deploy subnets across multiple Availability Zones.

---

## Architecture

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

## Why Multiple Availability Zones?

Deploying resources across multiple Availability Zones provides:

- High Availability
- Fault Tolerance
- AWS Well-Architected compliance

---

## Implementation Steps

### Step 1. Open the VPC Console

Navigate to:

```
VPC
→ Subnets
```

Click:

```
Create subnet
```


---

### Step 2. Select the VPC

Choose:

```
AI-SupplyChain-VPC
```

---

### Step 3. Create Public Subnet A

| Property | Value |
|----------|-------|
| Name | Public-Subnet-A |
| Availability Zone | ap-southeast-1a |
| IPv4 CIDR | 10.0.1.0/24 |

---

### Step 4. Create Public Subnet B

| Property | Value |
|----------|-------|
| Name | Public-Subnet-B |
| Availability Zone | ap-southeast-1b |
| IPv4 CIDR | 10.0.2.0/24 |

---

### Step 5. Create Private Subnet A

| Property | Value |
|----------|-------|
| Name | Private-Subnet-A |
| Availability Zone | ap-southeast-1a |
| IPv4 CIDR | 10.0.11.0/24 |

---

### Step 6. Create Private Subnet B

| Property | Value |
|----------|-------|
| Name | Private-Subnet-B |
| Availability Zone | ap-southeast-1b |
| IPv4 CIDR | 10.0.12.0/24 |

---

### Step 7. Verify the Result

The subnet list should display:

| Name | CIDR | AZ |
|------|------|----|
| Public-Subnet-A | 10.0.1.0/24 | ap-southeast-1a |
| Public-Subnet-B | 10.0.2.0/24 | ap-southeast-1b |
| Private-Subnet-A | 10.0.11.0/24 | ap-southeast-1a |
| Private-Subnet-B | 10.0.12.0/24 | ap-southeast-1b |



---

## Best Practices

- Use Public Subnets only for internet-facing resources.
- Deploy databases in Private Subnets.
- Create at least two Private Subnets across different Availability Zones.

---

## Expected Outcome

After completing this section, you have successfully:

- Created four Subnets.
- Separated public and private networks.
- Prepared the networking layer for Amazon RDS and backend services.