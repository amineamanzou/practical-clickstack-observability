---
title: "Incremental Materialized View Architecture"
weight: 2
description: "Understanding the Source-Trigger-Target architecture of ClickHouse Materialized Views."
---

This note details the mechanical flow of data through an Incremental Materialized View pipeline.

## The "TO" Target Architecture

**Question:** How does an Incremental Materialized View process data without scanning the entire source table?

**Answer Principle:** The architecture consists of three components: the **Source Table** (receives inserts), the **MV Trigger** (transformation logic), and the **Target Table** (stores results). The MV detects `INSERT` events on the source and transforms *only* the new block of data in memory before writing it to the target.

**Explanation:**

1. **Source Table (`raw_logs`):**
    * Receives the initial `INSERT` stream.
    * The MV does *not* read existing data from this table; it intercepts the write stream.

2. **The View (Trigger):**
    * Defined with a `SELECT` statement.
    * Query runs *once* per inserted block.
    * *Syntax:* `CREATE MATERIALIZED VIEW mv_name TO target_table AS SELECT ...`

3. **Target Table (`processed_logs`):**
    * Stores the transformed/aggregated data.
    * Has its own independent schema, engine, and partition key.
    * Querying this table is fast because the data is pre-calculated.

**Reference:**

* Title: [Materialized Views](https://clickhouse.com/docs/en/sql-reference/statements/create/view#materialized-view)
