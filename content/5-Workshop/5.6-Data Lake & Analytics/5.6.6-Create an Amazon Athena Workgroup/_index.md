---
title: "5.6.6 Create an Amazon Athena Workgroup"
date: 2024-01-01
weight: 6
chapter: false
---

# 5.6.6 Create an Amazon Athena Workgroup

## Overview

Amazon Athena provides **Workgroups** to separate workloads, manage query settings, and control costs.

Each Workgroup can have its own:

- Query result location
- Encryption settings
- Query limits
- CloudWatch metrics
- IAM permissions

In this workshop, you will create a dedicated Workgroup for the AI Supply Chain Control Tower.

---

## Architecture

```text
User
 │
 ▼
Athena Workgroup
 │
 ├── Query Engine
 ├── Query History
 ├── CloudWatch Metrics
 ├── Result Configuration
 │
 ▼
Amazon S3
athena-results/
```

---

## Objectives

After completing this section, you will:

- Create a dedicated Athena Workgroup.
- Configure the query result location.
- Enable query result encryption.
- Review query history.
- Prepare an isolated analytics environment.

---

# Step 1. Open Amazon Athena

Sign in to the AWS Management Console.

Search for:

```text
Amazon Athena
```

---

# Step 2. Open Workgroups

Choose:

```text
Workgroups
```

Then choose:

```text
Create workgroup
```

---

# Step 3. Configure the Workgroup

Name

```text
SupplyChainAnalytics
```

Description

```text
Athena Workgroup for AI Supply Chain Control Tower
```

State

```text
Enabled
```

---

# Step 4. Configure Query Results

Query result location

```text
s3://ai-supplychain-datalake/athena-results/
```

Enable:

```text
Override client-side settings
```

---

# Step 5. Enable Encryption

Choose:

```text
SSE-S3
```

or

```text
SSE-KMS
```

For production environments, AWS recommends:

```text
SSE-KMS
```

---

# Step 6. Enable CloudWatch Metrics

Enable:

```text
Publish query metrics
```

CloudWatch will collect:

```text
Query Duration

Query Count

Bytes Scanned

Failed Queries
```

---

# Step 7. Select the Workgroup

Return to the Athena Query Editor.

Switch to:

```text
SupplyChainAnalytics
```

---

# Step 8. Run a Test Query

Execute:

```sql
SELECT COUNT(*)
FROM orders_partitioned;
```

Expected result:

```text
Query completed successfully
```

---

# Step 9. Review Query History

Open:

```text
Recent Queries
```

Verify:

```text
Execution Time

Data Scanned

Query State
```

---

## Best Practices

- Use separate Workgroups for different environments.
- Always enable encryption.
- Store query results in a dedicated Amazon S3 location.
- Review query history regularly.
- Monitor scanned data to optimize query costs.
- Configure data usage controls or per-query limits for production environments.
- Grant Workgroup access through IAM roles following the principle of least privilege.

---

## Verification

Verify that:

- The Workgroup has been created.
- The query result location is configured correctly.
- Query result encryption is enabled.
- The Workgroup is active.
- Queries execute successfully.
- Query history records are visible.

---

## Expected Outcome

After completing this section, you have:

- Created an Amazon Athena Workgroup.
- Configured a dedicated query result location.
- Enabled query result encryption.
- Prepared a secure and isolated analytics environment.
- Established a foundation for monitoring Athena query performance and costs.
