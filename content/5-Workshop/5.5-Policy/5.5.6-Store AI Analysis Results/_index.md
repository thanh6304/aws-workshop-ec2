---
title: "5.5.6 Store AI Analysis Results"
date: 2024-01-01
weight: 6
chapter: false
---


## Overview

After the AI Worker Lambda receives analysis results from Amazon Bedrock, the generated insights are stored in Amazon RDS PostgreSQL.

Persisting AI results enables users to review previous analyses, monitor processing status, and display reports through the application dashboard.

---

## Architecture

```
Amazon Bedrock
        │
        ▼
AI Worker Lambda
        │
        ▼
Amazon RDS PostgreSQL
        │
        ▼
Dashboard / Backend API
```

---

## Objectives

After completing this section, you will:

- Create a database table for AI results.
- Track request status.
- Persist AI-generated recommendations.
- Verify stored records.

---

## Processing Status

Each request progresses through the following states:

```
PENDING
    │
    ▼
PROCESSING
    │
 ┌──┴─────┐
 ▼        ▼
COMPLETED FAILED
```

---

## Example Table

```sql
CREATE TABLE ai_analysis_results (
    id BIGSERIAL PRIMARY KEY,
    request_id VARCHAR(100) UNIQUE NOT NULL,
    warehouse VARCHAR(50),
    request_type VARCHAR(50),
    status VARCHAR(20),
    summary TEXT,
    recommendation TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP
);
```

---

## Processing Flow

1. Insert a new record with status **PENDING**.
2. Update the status to **PROCESSING** when the AI Worker starts.
3. Save the AI summary and recommendation.
4. Update the status to **COMPLETED**.
5. If processing fails, update the status to **FAILED**.

---

## Verification

Execute:

```sql
SELECT *
FROM ai_analysis_results
ORDER BY created_at DESC;
```

Verify that completed AI requests contain summaries and recommendations.

---

## Best Practices

- Use a unique request ID.
- Record processing timestamps.
- Index frequently queried columns.
- Avoid storing unnecessary prompt content.
- Store only the final AI output required by the application.

---

## Expected Outcome

After completing this section, AI analysis results are persisted in Amazon RDS PostgreSQL, enabling dashboards, reporting features, and historical analysis.---
title: "5.5.6 Store AI Analysis Results"
date: 2024-01-01
weight: 6
chapter: false

---

# 5.5.6 Store AI Analysis Results

## Overview

After the AI Worker Lambda receives analysis results from Amazon Bedrock, the generated insights are stored in Amazon RDS PostgreSQL.

Persisting AI results enables users to review previous analyses, monitor processing status, and display reports through the application dashboard.

---

## Architecture

```
Amazon Bedrock
        │
        ▼
AI Worker Lambda
        │
        ▼
Amazon RDS PostgreSQL
        │
        ▼
Dashboard / Backend API
```

---

## Objectives

After completing this section, you will:

- Create a database table for AI results.
- Track request status.
- Persist AI-generated recommendations.
- Verify stored records.

---

## Processing Status

Each request progresses through the following states:

```
PENDING
    │
    ▼
PROCESSING
    │
 ┌──┴─────┐
 ▼        ▼
COMPLETED FAILED
```

---

## Example Table

```sql
CREATE TABLE ai_analysis_results (
    id BIGSERIAL PRIMARY KEY,
    request_id VARCHAR(100) UNIQUE NOT NULL,
    warehouse VARCHAR(50),
    request_type VARCHAR(50),
    status VARCHAR(20),
    summary TEXT,
    recommendation TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    completed_at TIMESTAMP
);
```

---

## Processing Flow

1. Insert a new record with status **PENDING**.
2. Update the status to **PROCESSING** when the AI Worker starts.
3. Save the AI summary and recommendation.
4. Update the status to **COMPLETED**.
5. If processing fails, update the status to **FAILED**.

---

## Verification

Execute:

```sql
SELECT *
FROM ai_analysis_results
ORDER BY created_at DESC;
```

Verify that completed AI requests contain summaries and recommendations.

---

## Best Practices

- Use a unique request ID.
- Record processing timestamps.
- Index frequently queried columns.
- Avoid storing unnecessary prompt content.
- Store only the final AI output required by the application.

---

## Expected Outcome

After completing this section, AI analysis results are persisted in Amazon RDS PostgreSQL, enabling dashboards, reporting features, and historical analysis.
