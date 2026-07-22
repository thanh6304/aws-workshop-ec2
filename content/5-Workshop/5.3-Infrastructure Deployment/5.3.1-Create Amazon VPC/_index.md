---
title: "5.3.1 Create Amazon VPC"
date: 2024-01-01
weight: 1
chapter: false
---

# 5.3.1 Create Amazon VPC

## Overview

Amazon Virtual Private Cloud (Amazon VPC) enables you to create an isolated virtual network in AWS where cloud resources can securely communicate.

In the **AI Supply Chain Control Tower**, the VPC serves as the networking foundation for backend services, databases, and analytics components.

After completing this section, all infrastructure components will be deployed inside this VPC.

---

## Objectives

In this section, you will:

- Create an Amazon VPC.
- Configure an IPv4 CIDR Block.
- Enable DNS Resolution.
- Enable DNS Hostnames.
- Verify the VPC configuration.

---

## Architecture

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

## Implementation Steps

### Step 1. Open the VPC Console

Sign in to the AWS Management Console.

Search for:

```
VPC
```

Open the **VPC Dashboard**.

<center>

![Kiến trúc Amazon VPC](/images/5-Workshop/5.3-S3-vpc/5.3.1.png)

</center>

<p align="center">
  <b>Hình 5.3.1.</b> Kiến trúc Amazon VPC của hệ thống AI Supply Chain Control Tower.
</p>
---

### Step 2. Create a VPC

Click:

```
Create VPC
```

---

### Step 3. Configure the VPC

Configure the following settings:

| Property            | Value              |
| ------------------- | ------------------ |
| Resources to create | VPC only           |
| Name tag            | AI-SupplyChain-VPC |
| IPv4 CIDR           | 10.0.0.0/16        |
| IPv6 CIDR           | No IPv6            |
| Tenancy             | Default            |

> Insert Screenshot: Create VPC

---

### Step 4. Create the VPC

Click:

```
Create VPC
```

AWS will provision the virtual network.

---

### Step 5. Enable DNS Settings

Select the newly created VPC.

Verify that:

- DNS Resolution = Enabled
- DNS Hostnames = Enabled

If necessary:

```
Actions
Edit VPC Settings
```

Enable both options.

> Insert Screenshot: DNS Settings

---

### Step 6. Verify the Result

The VPC list should display:

| Name               | CIDR        |
| ------------------ | ----------- |
| AI-SupplyChain-VPC | 10.0.0.0/16 |

---

## Expected Outcome

After completing this section, you have successfully:

- Created an Amazon VPC.
- Configured the IPv4 CIDR block.
- Enabled DNS Resolution.
- Enabled DNS Hostnames.

This VPC will be used in the following sections to deploy Subnets, Security Groups, and Amazon RDS.
