---
title: "Granules and Sparse Indexing"
weight: 3
description: "The mechanism behind ClickHouse's fast query performance using sparse primary indexes."
---

This note details how ClickHouse avoids the cost of B-Tree indexing by using Granules and Sparse Indexes.

## Granules and Sparse Indexing

**Question:** Why does ClickHouse use a sparse index instead of indexing every row, and what is a Granule?

**Answer Principle:** Indexing every row is not scalable for petabytes of data (RAM overhead). ClickHouse uses a **Sparse Index** where one index entry represents a block of **8,192 rows** (a **Granule**). This allows the entire index to fit in memory.

**Explanation:**

1. **The Scaling Problem:**
    * In traditional OLTP databases (B-Tree), every row is indexed.
    * For 1 Trillion rows, indexing every row would require ~7.3 TiB of RAM just for the index.
    * ClickHouse's sparse index for 1 Trillion rows requires only ~931 MiB of RAM.

2. **Granules:**
    * A **Granule** is the smallest indivisible amount of data ClickHouse scans.
    * Default size: **8,192 rows**.
    * Granules are logical groupings inside a Data Part.

3. **How the Sparse Index Works:**
    * The Primary Key stores the value of the **first row** of each granule.
    * **Data skipping:** When a query arrives (e.g., `WHERE timestamp > X`), ClickHouse checks the sparse index.
    * If a granule's range does not match the query, the *entire* 8,192 rows are skipped without reading from the disk.
    * If a match is possible, the entire granule is loaded and scanned.

**Reference:**

* Title: [Sparse Primary Indexes](https://clickhouse.com/docs/en/optimize/sparse-primary-indexes)
