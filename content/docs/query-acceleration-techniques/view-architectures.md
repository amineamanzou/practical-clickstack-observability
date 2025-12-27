---
title: "View Architectures: Standard, Refreshable, and Incremental"
weight: 1
description: "Comparing Standard Views, Refreshable Materialized Views, and Incremental Materialized Views."
---

This note classifies the three distinct types of view objects available in ClickHouse and their execution models.

## View Classifications

**Question:** What are the differences between a Normal View, a Refreshable Materialized View, and an Incremental Materialized View?

**Answer Principle:**

1. **Normal View:** A virtual saved query alias executed at read time.
2. **Refreshable Materialized View:** A scheduled task (CRON-like) that periodically re-calculates the query and atomically replaces the target table.
3. **Incremental Materialized View:** An **insert trigger** that processes new data blocks in real-time as they are ingested.

**Explanation:**

1. **Normal View (`CREATE VIEW`):**
    * Does not store data.
    * Acts as a saved SQL alias or macro.
    * *Cost:* The query is executed against the source table every time the view is accessed.

2. **Refreshable Materialized View:**
    * *Syntax:* `CREATE MATERIALIZED VIEW ... REFRESH EVERY ...`
    * Executes the defined query periodically (e.g., every 1 minute).
    * Atomically replaces the target table with the new result.
    * *Use Case:* Small datasets or complex aggregations that cannot be computed incrementally.

3. **Incremental Materialized View:**
    * The standard/default ClickHouse MV.
    * Functions as an **Insert Trigger**.
    * Processes only the *newly inserted block* of data and appends results to the target table.

**Reference:**

* Title: [Views in ClickHouse](https://clickhouse.com/docs/en/sql-reference/statements/create/view)
