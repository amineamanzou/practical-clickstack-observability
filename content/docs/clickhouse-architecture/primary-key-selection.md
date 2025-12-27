---
title: "Optimizing Primary Keys"
weight: 4
description: "Best practices for choosing and ordering columns in a ClickHouse Primary Key."
---

This note provides guidance on selecting the optimal Primary Key to maximize data skipping.

## Primary Key Optimization

**Question:** How should columns be ordered in a Primary Key, and does the primary key ensure uniqueness?

**Answer Principle:** Primary Keys in `MergeTree` are **not unique**. Columns should be ordered by **Cardinality (Lowest to Highest)** and by **Query Frequency**. This ordering ensures the most effective data skipping (filtering out large ranges of granules).

**Explanation:**

1. **Sorting and Indexing:**
    * The `ORDER BY` clause determines how data is sorted on disk.
    * The `PRIMARY KEY` determines the sparse index. (Usually, they are the same, but `ORDER BY` can extend the `PRIMARY KEY`).

2. **Cardinality Rule:**
    * Order columns by **ascending cardinality** (fewer unique values first).
    * **Example:** If you filter by `Environment` (3 values) and `Timestamp` (infinite values), put `Environment` first.
    * This groups all `Production` logs together physically. A query for `Staging` can skip gigabytes of `Production` data immediately.

3. **Key Length:**
    * Don't add columns unnecessarily.
    * Only add a column if it is frequently queried **AND** adding it allows ClickHouse to skip long data ranges.

4. **Secondary Indexing Options:**
    If the Primary Key doesn't fit a specific query pattern, use:
    * **Projections:** Hidden tables with different sort orders.
    * **Materialized Views:** Separate tables populated by triggers with different keys.
    * **Skipping Indexes:** Lightweight bloom filters or min/max indices.

**Reference:**

* Title: [Primary Keys Best Practices](https://clickhouse.com/docs/en/optimize/sparse-primary-indexes)
