---
title: "ClickHouse Table Engines and Architecture"
weight: 1
description: "Overview of ClickHouse logical structure, database grouping, and the role of Table Engines."
---

This note outlines the fundamental architectural hierarchy of ClickHouse and the critical role of Table Engines in determining storage behavior.

## Table Engines and Architecture

**Question:** What determines how and where data is stored in ClickHouse, and what is the difference between using ClickHouse as a database versus a query engine?

**Answer Principle:** The **Table Engine** determines physical storage and retrieval mechanisms. ClickHouse acts as a **Database** when managing its own data via `MergeTree` engines, and as a **Query Engine** when accessing external systems (like S3 or Kafka) without owning the storage.

**Explanation:**

1.  **Logical Hierarchy:**
    *   **Databases:** ClickHouse groups tables logically into databases (e.g., `CREATE DATABASE observability;`).
    *   **Tables:** Inside databases, data is organized into tables.

2.  **The Role of Table Engines:**
    Every table must have an engine defined (`ENGINE = ...`). This engine dictates:
    *   Where the data is stored (Local disk, S3, Memory, etc.).
    *   How the data is accessed (Column-oriented, external stream).
    *   Concurrent access rules.

3.  **Database vs. Query Engine:**
    *   **As a Database:** Uses the `MergeTree` family. Data is stored on the ClickHouse node's disk (or tiered storage). It provides column-oriented, highly scalable performance.
    *   **As a Query Engine:** Uses integration engines (e.g., `S3`, `Kafka`, `HDFS`, `PostgreSQL`). ClickHouse processes the query but fetches data from an external source on the fly.

4.  **MergeTree Family:**
    `MergeTree` is the default engine for single-node high performance. Variants include:
    *   `ReplacingMergeTree`: For upsert/deduplication scenarios.
    *   `AggregatingMergeTree`: For pre-calculated roll-ups.
    *   `ReplicatedMergeTree`: For high availability (requires ZooKeeper/ClickHouse Keeper).
    *   `SharedMergeTree`: Used in ClickHouse Cloud.

**Reference:**

- Title: [Table Engines in ClickHouse](https://clickhouse.com/docs/en/engines/table-engines)