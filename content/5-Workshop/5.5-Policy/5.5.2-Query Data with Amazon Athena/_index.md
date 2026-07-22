---
title: "5.5.2 Query Data with Amazon Athena"
date: 2024-01-01
weight: 2
chapter: false
---


## Overview

Amazon Athena is a serverless interactive query service that enables SQL queries directly against data stored in Amazon S3.

In the **AI Supply Chain Control Tower**, Athena queries datasets registered in AWS Glue Data Catalog. The query results are then consumed by the AI Worker Lambda and sent to Amazon Bedrock for intelligent analysis.

---

## Architecture

```
Amazon S3 Data Lake
        │
        ▼
AWS Glue Data Catalog
        │
        ▼
Amazon Athena
        │
        ▼
Lambda Worker
        │
        ▼
Amazon Bedrock
```

---

## Objectives

After completing this section, you will:

- Configure Amazon Athena.
- Set a query result location.
- Execute SQL queries.
- Analyze supply chain datasets.
- Prepare data for Amazon Bedrock.

---

## Implementation Steps

1. Open the Amazon Athena Console.
![Amazon Athena Dashboard ](/images/5-Workshop/5.5-Policy/5.5.2.png)
2. Configure the query result location:

```
s3://ai-supplychain-datalake/athena-results/
```

3. Select:

- Data Source: `AwsDataCatalog`
- Database: `supplychain_catalog`

4. Execute SQL queries such as:

```sql
SELECT *
FROM inventory
LIMIT 10;
```

```sql
SELECT product_name, quantity
FROM inventory
WHERE quantity < 20;
```

```sql
SELECT COUNT(*)
FROM orders;
```

```sql
SELECT SUM(total_amount)
FROM orders;
```

5. Review the results and verify that Athena stores the query output in Amazon S3.

---

## Best Practices

- Select only the required columns.
- Store datasets in Parquet or ORC format for better performance.
- Partition large datasets to reduce query costs.
- Monitor scanned data volumes to optimize Athena pricing.

---

## Expected Outcome

After completing this section, you have successfully queried supply chain datasets stored in Amazon S3 and prepared structured data for AI analysis with Amazon Bedrock.
