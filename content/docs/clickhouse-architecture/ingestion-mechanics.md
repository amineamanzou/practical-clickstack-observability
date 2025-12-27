---
title: "Ingestion Risks: Partitioning vs. Batching"
weight: 10
description: "Understanding how granular partitioning can negate the benefits of batching during ingestion."
---

This note explains the mechanical interaction between insert batches and table partitions, highlighting a common performance pitfall.

## The Partition Splitting Effect

**Question:** Why does inserting a large, optimized batch of data result in a "Too many parts" error if the table is partitioned by a granular key (like `minute`)?

**Answer Principle:** ClickHouse inserts are atomic per partition. A single incoming batch is **physically split** into as many Data Parts as there are unique partition keys in that batch. Granular partitioning fractures large batches into tiny files, overwhelming the storage system.

**Explanation:**

1. **The Scenario:**
    * You create a table partitioned by `Minute`.
    * You buffer data in your collector and send a batch of **100,000 rows** covering the last hour.

2. **The Mechanical Failure:**
    * ClickHouse receives the batch but sees data for 60 different partitions (minutes).
    * It must perform **60 separate physical writes**, creating 60 tiny Data Parts (folders) instead of 1 large efficient one.
    * The background merge process cannot keep up with this explosion of file counts.

3. **The Consequence:**
    * Severely degraded ingestion performance.
    * `Too many parts` exception.

4. **Prevention:**
    * Use **Low Cardinality** partition keys (Day or Month).
    * Use the `max_partitions_per_insert_block` setting (default 100) to fail queries that attempt to explode part counts.

**Reference:**

* Title: [Partitioning Limits](https://clickhouse.com/docs/en/operations/settings/settings#max_partitions_per_insert_block)
