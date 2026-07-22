---
title: "5.7.6 Encrypt Data Using AWS Key Management Service (AWS KMS)"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.7.6 Encrypt Data Using AWS Key Management Service (AWS KMS)

## Overview

Encryption is a fundamental security control for protecting sensitive data in the cloud.

AWS Key Management Service (AWS KMS) enables you to create, manage, and control encryption keys that integrate with AWS services.

In this workshop, AWS KMS is used to encrypt:

- Amazon S3 Data Lake
- Amazon RDS PostgreSQL
- AWS Secrets Manager
- Amazon SNS
- Amazon CloudWatch Logs (optional)

---

## Architecture

```text
                AWS KMS
                   │
      Customer Managed Key (CMK)
                   │
      ┌────────────┼─────────────┐
      ▼            ▼             ▼
 Amazon S3     Amazon RDS   Secrets Manager
      │            │             │
      └────────────┼─────────────┘
                   ▼
      AI Supply Chain Control Tower
```

---

## Objectives

After completing this section, you will:

- Create a Customer Managed Key (CMK).
- Configure key administrators and users.
- Encrypt Amazon S3.
- Encrypt Amazon RDS.
- Encrypt AWS Secrets Manager.
- Verify encryption settings.

---

# Step 1. Open AWS KMS

Open the AWS Management Console.

Search for:

```text
Key Management Service
```

Open:

```text
AWS Key Management Service
```

---

# Step 2. Create a Customer Managed Key

Navigate to:

```text
Customer managed keys

Create key
```

Configuration:

| Property  | Value               |
| --------- | ------------------- |
| Key type  | Symmetric           |
| Key usage | Encrypt and decrypt |
| Origin    | AWS KMS             |

Alias:

```text
alias/supplychain-key
```

Description:

```text
Encryption key for AI Supply Chain Control Tower
```

Create the key.

---

# Step 3. Configure Key Administrators

Assign an IAM administrator such as:

```text
SupplyChainAdmin
```

This principal can manage the key, edit key policies, and enable key rotation.

---

# Step 4. Configure Key Users

Grant usage permissions to:

```text
BackendLambdaRole

AIWorkerLambdaRole
```

These roles can use the key for encryption and decryption operations.

---

# Step 5. Encrypt Amazon S3

Open the bucket:

```text
ai-supplychain-datalake
```

Navigate to:

```text
Properties

Default encryption
```

Select:

```text
AWS KMS Key

alias/supplychain-key
```

---

# Step 6. Verify Amazon RDS Encryption

Open the RDS instance.

Verify:

```text
Storage Encryption

Enabled
```

For new databases, select:

```text
Enable encryption

AWS KMS Key

alias/supplychain-key
```

---

# Step 7. Verify AWS Secrets Manager

Open:

```text
SupplyChainDatabaseSecret
```

Confirm that the encryption key is set to:

```text
alias/supplychain-key
```

---

# Step 8. Encrypt Amazon SNS (Optional)

Open:

```text
SupplyChainAlerts
```

Enable:

```text
Server-side encryption
```

Select:

```text
alias/supplychain-key
```

---

# Step 9. Review the KMS Key

Verify:

- Key ID
- Alias
- Rotation Status
- Key Policy
- Key Users

---

## Best Practices

- Use Customer Managed Keys for sensitive workloads.
- Enable automatic key rotation.
- Limit key administrators and users.
- Use separate keys for development, testing, and production.
- Audit key usage with AWS CloudTrail.

---

## Verification

Verify that:

- The Customer Managed Key has been created.
- Amazon S3 uses the KMS key.
- Amazon RDS storage encryption is enabled.
- AWS Secrets Manager uses the Customer Managed Key.
- Amazon SNS encryption is enabled if configured.

---

## Expected Outcome

After completing this section, you have:

- Created a Customer Managed Key using AWS KMS.
- Encrypted critical AWS resources.
- Improved the protection of sensitive data.
- Established a stronger security foundation for the AI Supply Chain Control Tower.
