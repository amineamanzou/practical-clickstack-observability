---
title: "Atomic Schema Migration (Exchange Strategy)"
weight: 10
description: "A strategy for modifying immutable table properties (like Primary Keys) using Atomic Exchange."
---

In ClickHouse, the `PRIMARY KEY` and `ORDER BY` tuple are physical properties of the data on disk and cannot be altered on a populated table. You must use a migration pattern involving a new table and an atomic swap.

## The Exchange Pattern

**Question:** How do you safely change the Primary Key of a populated table without downtime or data loss?

**Answer Principle:** Create a new table with the **desired** schema, then use `EXCHANGE TABLES` to atomically swap the names of the new and old tables.

**Explanation:**
The workflow ensures zero downtime and data consistency:

1. **Create New Table:** Define a new table (e.g., `otel_logs_original_primary_key`) with the **new** Primary Key definition.
    * *Note:* We name it `...original_primary_key` because *after* the swap, this table will hold the data with the old (original) key.
2. **Exchange:** Run the swap command.

    ```sql
    EXCHANGE TABLES default.otel_logs_original_primary_key AND default.otel_logs;
    ```

**Why Exchange instead of Rename?**
`EXCHANGE TABLES` is **atomic**. It swaps the metadata pointers instantly.

* **Rename Risk:** Using `RENAME` (e.g., Rename A->Old, Rename B->A) is non-atomic. It creates a split-second gap where table "A" does not exist, causing data loss for high-throughput ingestion streams.

**Reference:**

* Title: [EXCHANGE TABLES | ClickHouse Docs](https://clickhouse.com/docs/en/sql-reference/statements/exchange-tables)
