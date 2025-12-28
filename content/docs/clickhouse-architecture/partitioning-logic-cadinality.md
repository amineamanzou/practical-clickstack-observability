---
title: "Partitioning Logic: High Cardinality Risks"
weight: 12
description: "Evaluation of partitioning keys, the distinction between Primary Keys and Partition Keys, and the catastrophic performance impact of high-cardinality partitioning (e.g., Timestamp)."
---

Introduction Selecting a partition key is one of the few irreversible decisions in ClickHouse table design (without table rewriting). This note provides the logical framework for choosing a key that balances data lifecycle management with storage efficiency, specifically warning against the common pitfall of high-cardinality keys.

## Partitioning Logic and Risks

**Question:** Why does partitioning by a high-granularity column (like Timestamp or TraceId) degrade performance and cause the "Too Many Parts" exception, whereas partitioning by toDate() stabilizes the system?

**Answer Principle:**

Partitions are logical divisions designed for data lifecycle management (dropping old data) and reducing read I/O (pruning). However, because merges only occur within a partition, a high-cardinality partition key fragments data into thousands of isolated buckets that can never be merged, leading to metadata overhead and file system saturation.

**Explanation:**

1. The Role of the Partition Key In ClickHouse, the PARTITION BY clause is optional. Its primary purposes are:

    * TTL Management: It is instantaneous to DROP PARTITION. This enables efficient deletion of old logs (e.g., dropping 2023-10-01).
    * Pruning: Queries filtering on the partition key allow ClickHouse to skip entire directories.

2. The High Cardinality Trap In the lab, PARTITION BY Timestamp caused failure.

    * Granularity: Second-level precision creates a unique partition key for every second.
    * Isolation: Parts from different partitions cannot be merged.
    * Limit Breach: ClickHouse recommends keeping total partitions < 1,000. Timestamp partitioning breaches this in minutes.

3. Resource Exhaustion Vectors

    * Inode Exhaustion: 10,000 parts can generate 100,000+ files.
    * Coordination Load: Replicated environments suffer from excessive Zookeeper commits.
    * Query Latency: SELECT * must open every active part. Opening 1,830 files takes significantly longer than opening 4.

4. The Correct Approach: toDate() or toYYYYMM() Switching to toDate(Timestamp) resolves the issue:

    * Cardinality Reduction: 10 years of data = ~3,650 partitions.
    * Merge Efficiency: Logs from the same day land in the same partition, allowing aggressive compaction into large parts (up to 150GB).
    * Primary Key for Granularity: Use the Primary Key (ORDER BY) for millisecond-level filtering, not the Partition Key.

**References:**

* [Choosing a Partition Key](https://clickhouse.com/docs/best-practices/choosing-a-partitioning-key)
* [Partitioning Mechanics: Custom Partitioning](https://clickhouse.com/docs/engines/table-engines/mergetree-family/custom-partitioning-key)
