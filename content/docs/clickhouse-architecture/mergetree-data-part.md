---
title: "MergeTree Data Part Structure and Immutability"
weight: 11
description: "Detailed analysis of how ClickHouse persists data into immutable parts, the internal file structure of Wide vs. Compact parts, and the role of the primary key index."
---

## Introduction

The fundamental unit of storage in ClickHouse is the "Part." Understanding the anatomy of a part—how it is created, how it is structured on disk, and why it is immutable—is a prerequisite for diagnosing performance issues. This note dissects the physical layout of MergeTree tables and explains why "Too Many Parts" is a symptom of write amplification unchecked by compaction.

## MergeTree Data Part Structure

**Question:** How does the MergeTree engine persist data to disk, and why is the concept of "immutability" central to understanding the "Too Many Parts" error?

**Answer Principle:** ClickHouse does not append data to existing files. Instead, every INSERT operation creates a new, immutable directory (a "part") containing sorted data. These parts are only removed or consolidated during background merge processes. High-cardinality partitioning breaks this process by isolating parts in unique partitions, preventing the merge scheduler from combining them.

**Explanation:**

1. The Immutable Log-Structured Storage Model Unlike B-Tree based relational databases that modify pages in place, ClickHouse utilizes a storage mechanism inspired by Log-Structured Merge (LSM) trees. When data is ingested into a table like otel_logs, it is written to a new folder on the filesystem.

    * Atomic Ingestion: A single INSERT block generates at least one new data part.
    * Sorting: Within this directory, data is lexicographically sorted by the Primary Key (ORDER BY).

2. Physical Structure: Wide vs. Compact Parts The internal structure of these part directories changes based on the size of the data insertion.

    * Compact Parts: For small inserts (typically < 10MB or < 1000 rows), all columns are stored in a single data.bin file alongside a data.mrk3 mark file. This reduces inode usage.
    * Wide Parts: For larger inserts, each column is stored in its own file (e.g., Timestamp.bin, SeverityText.bin). This allows queries to read only the specific column files they need.

3. The Merge Process (Compaction) The system runs background Merge threads to prevent file system exhaustion.

    * Selection: The scheduler selects overlapping parts.
    * Fusion: It merges them into a new, larger part.
    * Atomic Switch: The old parts are marked "inactive" and deleted.
    * Mermaid Diagram

    The Merge Cycle The following diagram illustrates the lifecycle of data moving from ingestion to compaction.

    ```mermaid
    graph TD
        subgraph Ingestion
        I -->|Batch 1| P1[Part_1_1_0]
        I -->|Batch 2| P2[Part_2_2_0]
        I -->|Batch 3| P3[Part_3_3_0]
        end

        subgraph "Background Merge Process"
        P1 & P2 & P3 -->|Selection| M
        M -->|Compaction| P_New[Part_1_3_1]
        end

        subgraph "State Transition"
        P_New -->|Becomes Active| Active
        P1 -->|Marked Inactive| Inactive
        P2 -->|Marked Inactive| Inactive
        P3 -->|Marked Inactive| Inactive
        Inactive -->|old_parts_lifetime| Garbage[Garbage Collector]
        end

        style P_New fill:#bbf,stroke:#333,stroke-width:2px
        style Active fill:#bfb,stroke:#333,stroke-width:2px
        style Garbage fill:#f99,stroke:#333,stroke-width:2px
    ```

4. The Immutability Implication Write amplification is inherent. Data is written once during insert and rewritten multiple times during merges. Error 252 ("Too Many Parts") occurs when the rate of creation exceeds the rate of compaction. Partitioning by Timestamp forces isolation, rendering the merge process useless as parts cannot be merged across different partitions.

## References

* [System Parts Table](https://clickhouse.com/docs/operations/system-tables/parts)
* [MergeTree Family](https://clickhouse.com/docs/engines/table-engines/mergetree-family/mergetree)
* [Too Many Parts Error](https://clickhouse.com/docs/tips-and-tricks/too-many-parts)
