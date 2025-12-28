---
title: "LowCardinality Data Type"
weight: 15
description: "Optimizing storage and performance for string columns with few unique values."
---

When storing repetitive string data, standard string types can be inefficient regarding storage space and query speed.

## LowCardinality(String)

**Question:** How should string columns with a low number of unique values (relative to the total row count) be typed to optimize storage?

**Answer Principle:** Use the `LowCardinality(String)` data type, which internally uses dictionary encoding to compress data and speed up operations.

**Explanation:**
Columns like `City`, `CountryCode`, or `Environment` often have high repetition. The `LowCardinality` type creates a local dictionary for the column, replacing long strings with small integer keys.

This offers two benefits:

1. **Storage Efficiency:** Reduces the disk footprint of the table.
2. **Performance:** Operations like `GROUP BY` or `FILTER` are performed on the integer keys rather than the full strings, which is significantly faster.

**Code Example:**

```sql
-- CountryCode is ideal for LowCardinality as there are few countries compared to millions of logs
ALTER TABLE otel_logs 
ADD COLUMN CountryCode LowCardinality(String) 
MATERIALIZED dictGetOrDefault('ip_trie', 'country_code', RemoteAddress, 'ZZ');
```

**References:**

* [ClickHouse Documentation: LowCardinality](https://clickhouse.com/docs/sql-reference/data-types/lowcardinality)
