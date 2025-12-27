---
title: "Mechanics of Query Acceleration"
weight: 8
description: "Understanding the physical reason why AggregatingMergeTree provides exponential performance gains."
---

This note explains the theoretical basis for the speed of Materialized Views, focusing on the "Row Reduction Ratio."

## Cardinality and Row Reduction

**Question:** Why exactly is querying a pre-aggregated table orders of magnitude faster than querying the raw logs?

**Answer Principle:** Performance in ClickHouse is primarily bound by the **number of rows scanned**. Aggregation reduces the row count from **Ingestion Scale** (total events) to **Cardinality Scale** (number of unique grouping keys).

**Explanation:**

1. **Raw Table Physics:**
    * 1 Billion log events = 1 Billion rows to scan for `avg(latency)`.
    * Performance cost is linear with data volume.

2. **Aggregated Table Physics:**
    * If you group by `log_level` (which has only ~4 unique values: INFO, WARN, ERROR, DEBUG) and partition by hour.
    * The `AggregatingMergeTree` collapses the 1 Billion rows into just **4 rows** per hour (after background merges).
    * Scanning 4 rows is effectively instantaneous.

3. **The "Cardinality Rule":**
    * This technique *only* works if the number of unique grouping keys (Cardinality) is significantly smaller than the total number of rows.
    * *Anti-Pattern:* Grouping by unique `TraceID` results in no row reduction and doubles the storage cost.

**Reference:**

* Title: [Materialized View Performance](https://clickhouse.com/docs/en/guides/developer/casestudies/materialized-views-performance)
