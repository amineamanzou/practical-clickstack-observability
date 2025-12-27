---
title: "Table Partitioning Strategy"
weight: 5
description: "Understanding when to use partitioning versus primary keys."
---

This note differentiates between Partitioning for data management and Primary Keys for query performance.

## Table Partitioning

**Question:** Should I partition my table by minute or hour to improve query performance?

**Answer Principle:** **No.** Partitioning is primarily for **Data Management** (TTL, dropping old data), not query performance. Over-partitioning creates too many small parts and degrades performance. Always prefer a good Primary Key for query speed.

**Explanation:**

1. **Definition:**
    * A partition is a logical combination of records (e.g., all data for "Day 1").
    * Operations like `DROP PARTITION` are atomic and instant.

2. **When to Partition:**
    * **Observability/Logs:** Partition by **Day** (recommended default) or **Month**.
    * **Massive Data:** Partition by **Hour** only for extremely high-volume use cases.

3. **The Pitfall of Granular Partitioning:**
    * Creating a partition key on high-cardinality data (like `customer_id` or `timestamp` by minute) creates thousands of folders.
    * ClickHouse limits inserts to a max of 100 partitions per block (`max_partitions_per_insert_block`) to prevent file system overload.

4. **Recommendation:**
    * Focus on the **Primary Key** for filtering speed.
    * Use **Partitions** for data retention policies (e.g., "Delete data older than 30 days").

**Reference:**

* Title: [Custom Partitioning Key](https://clickhouse.com/docs/en/engines/table-engines/mergetree-family/custom-partitioning-key)
