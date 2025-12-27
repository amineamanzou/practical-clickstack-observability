---
title: "Data Parts and Storage Structure"
weight: 2
description: "Understanding how ClickHouse handles inserts, data parts, and background merges."
---

This note explains the physical storage mechanism of ClickHouse, focusing on immutable data parts and the merge process.

## Data Parts and Merges

**Question:** How does ClickHouse handle high ingestion rates physically on the disk, and what is the cost of frequent small inserts?

**Answer Principle:** ClickHouse writes data to **immutable parts** (folders) per insert. To maintain performance, background processes **merge** these small parts into larger ones. Frequent small inserts cause "Write Amplification" due to excessive merging.

**Explanation:**

1.  **The Insert Process:**
    *   Each `INSERT` statement creates a new **Data Part**.
    *   A Data Part is physically a folder on the disk containing data and metadata.
    *   Columns are stored in separate, **immutable** files within that folder.
    *   Initially, data is not necessarily sorted or indexed across the whole table, only within that specific part.

2.  **The Merge Process:**
    *   Because opening thousands of files is slow, ClickHouse runs background **Merges**.
    *   Merges consolidate overlapping parts into larger, more efficient parts.
    *   **Resource Cost:** Merging consumes CPU (decompression/recompression), Memory (loading parts), and Disk I/O (reading/writing).

3.  **Performance Implication:**
    *   **Small Inserts:** Sending 1 event per insert creates 1 part folder per event. This leads to thousands of parts, forcing the server to merge aggressively, potentially stalling ingestion ("Too many parts" error).
    *   **Ideal Scenario:** Large batches (e.g., 100k rows) create fewer, larger parts, reducing merge overhead.

**Reference:**

- Title: [MergeTree Data Storage](https://clickhouse.com/docs/en/engines/table-engines/mergetree-family/mergetree)