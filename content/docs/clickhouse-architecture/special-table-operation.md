---
title: "Atomic Table Operations"
weight: 7
description: "Advanced commands for managing table structure and aliases."
---

This note covers specific SQL commands for atomic updates and table aliasing.

## Atomic Operations and Aliases

**Question:** How can you update a table's data entirely without downtime, and how can you create a table that reads from multiple other tables?

**Answer Principle:** Use `EXCHANGE TABLES` for atomic hot-swapping of data. Use the `Merge` table engine (not to be confused with `MergeTree`) to create a read-only alias that aggregates multiple tables.

**Explanation:**

1. **Atomic Swap (`EXCHANGE TABLES`):**
    * Command: `EXCHANGE TABLES new_table AND old_table;`
    * **Use Case:** You have a huge lookup table. You rebuild it in the background as `new_table`. You want to switch traffic to it instantly.
    * This operation is atomic; no queries will fail during the swap.

2. **Table Aliases (`Merge` Engine):**
    * Command: `CREATE TABLE combined_logs ENGINE = Merge('database', 'otel_logs*');`
    * **Use Case:** Reading from multiple tables (e.g., `otel_logs_v1` and `otel_logs_v2`) as if they were one.
    * **Features:**
        * Does not store data itself (virtual).
        * Parallelizes reading across the underlying tables.
        * **Read-only** (writing to a Merge table is not supported).

**Reference:**

* Title: [Merge Table Engine](https://clickhouse.com/docs/en/engines/table-engines/special/merge)
