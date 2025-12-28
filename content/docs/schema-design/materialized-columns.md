---
title: "ClickHouse Materialized Columns for Query Simplification"
weight: 11
description: "Using materialized columns to simplify syntax and optimize access for nested data structures in ClickHouse."
---

This note outlines the process of abstracting complex data extraction logic into dedicated columns to improve query readability and maintainability.

## Optimizing Map Data Access with Materialized Columns

**Question:** How can complex data extractions (e.g., specific keys from Maps or JSON) be simplified and optimized for repeated querying in ClickHouse?

**Answer Principle:** Use `MATERIALIZED` columns to define a specific extraction logic once at the table level. This computes the value automatically on insertion (or backfill), allowing users to query a simple column name instead of repeating complex extraction syntax.

**Explanation:**
In scenarios where data is stored in complex structures like `Map` or `JSON` (e.g., OpenTelemetry logs), queries often require repetitive and verbose extraction syntax.

1. **Identify the Access Pattern:**
    Initially, accessing specific attributes requires accessing the map directly.
    * *Example:* `ResourceAttributes['telemetry.sdk.language']`

2. **Define Materialized Columns:**
    You can alter the table to add a column with the `MATERIALIZED` keyword. This column does not physically store data provided by the user during insert; instead, it computes it based on the provided expression.
    * *Syntax:* `ALTER TABLE ... ADD COLUMN ... MATERIALIZED expression`

3. **Backfill Data (If needed):**
    For existing data, the `MATERIALIZE COLUMN` command triggers the calculation for rows already on disk.

4. **Result:**
    Queries become cleaner and potentially faster (if typed correctly, e.g., using `LowCardinality`), as the database reads the processed column directly.

**Process Flow:**

```mermaid
graph TD
    A[Raw Data Insert] --> B{Table Schema}
    B --> C[Standard Columns]
    B -->|Expression| D[Materialized Columns]
    D --> E[SDKLanguage]
    D --> F[SDKName]
    
    G[User Query] -->|Reads| E
    G -->|Reads| F
    G -.->|Avoids Complex Extraction| C
````

**Code Example:**

Before (Verbose):

```SQL
SELECT 
    ResourceAttributes['telemetry.sdk.language'] AS SDKLanguage,
    count()
FROM otel_logs
GROUP BY SDKLanguage;
```

Implementation:

```SQL
-- 1. Create the column definition
ALTER TABLE otel_logs
    ADD COLUMN SDKLanguage LowCardinality(String) 
    MATERIALIZED ResourceAttributes['telemetry.sdk.language'];

-- 2. Backfill existing data
ALTER TABLE otel_logs MATERIALIZE COLUMN SDKLanguage;
```

After (Simplified):

```SQL
SELECT 
    SDKLanguage,
    count()
FROM otel_logs
GROUP BY SDKLanguage;
```

## References

* [Materialized Columns](https://clickhouse.com/docs/en/sql-reference/statements/alter/column%23materialized-column)
