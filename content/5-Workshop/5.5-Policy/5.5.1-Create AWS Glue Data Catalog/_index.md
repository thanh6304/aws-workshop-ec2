---
title: "5.5.1 Create AWS Glue Data Catalog"
date: 2024-01-01
weight: 1
chapter: false
---


## Overview

AWS Glue Data Catalog is a centralized metadata repository that stores information about datasets located in Amazon S3.

In the **AI Supply Chain Control Tower**, the Data Catalog enables Amazon Athena to understand the schema of CSV and JSON files without loading them into a traditional database.

---

## Architecture

```
Amazon S3 Data Lake
        │
        ▼
AWS Glue Crawler
        │
        ▼
AWS Glue Data Catalog
        │
        ▼
Amazon Athena
```

---

## Objectives

After completing this section, you will:

- Create a Glue Database.
- Create a Glue Crawler.
- Scan datasets stored in Amazon S3.
- Generate metadata automatically.
- Prepare datasets for Amazon Athena.

---

## Implementation Steps

1. Open the AWS Glue Console.
![AWS Glue Dashboard ](/images/5-Workshop/5.5-Policy/5.5.1.png)
2. Create a Glue Database named:

```
supplychain_catalog
```

3. Create a Glue Crawler.
4. Select Amazon S3 as the data source:

```
s3://ai-supplychain-datalake/
```

5. Attach an IAM Role with access to Amazon S3 and AWS Glue.
6. Select the Glue Database.
7. Run the crawler.
8. Verify that tables are created successfully.

---

## Best Practices

- Organize S3 data using logical folder structures.
- Use columnar formats such as Parquet for production workloads.
- Re-run crawlers whenever dataset schemas change.
- Apply least-privilege IAM permissions.

---

## Expected Outcome

After completing this section, AWS Glue Data Catalog contains metadata for your datasets, allowing Amazon Athena to query business data stored in Amazon S3.
