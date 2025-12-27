---
title: "Operational TTL Management"
weight: 8
description: "How to manage, tune, and force the application of Time-To-Live policies on existing data."
---

This note explains the operational behavior of TTLs and the specific commands required to enforce them immediately on historical data.

## Applying and Tuning TTL

**Question:** After adding or modifying a TTL on a table, why is the old data not deleted immediately, and how can you force it?

**Answer Principle:** TTL changes (`ALTER TABLE`) update metadata but do not trigger immediate deletions. Actual deletion happens "lazily" during background merges. To force immediate application (e.g., to free disk space), run **`MATERIALIZE TTL`**. You can also tune the `merge_with_ttl_timeout` to control the frequency of deletion merges.

**Explanation:**

1. **Lazy Application:**
    * Command: `ALTER TABLE ... MODIFY TTL ...`
    * **Behavior:** Updates the table definition. Data expires only when ClickHouse naturally decides to merge data parts, which might take days or weeks for "cold" partitions.

2. **Forcing Deletion (`MATERIALIZE`):**
    * Command: `ALTER TABLE table_name MATERIALIZE TTL;`
    * **Effect:** Triggers an immediate mutation to scan data parts, identify expired rows/columns, and rewrite the parts without them.
    * **Cost:** High I/O and CPU resource usage. Use with caution on production clusters.

3. **Tuning Frequency:**
    * Setting: `merge_with_ttl_timeout` (Default: 14400s / 4 hours).
    * **Behavior:** Ensures that if no natural merges happen, ClickHouse will trigger an off-schedule merge at least this often to clean up expired data.

**Reference:**

* Title: [Materializing TTL](https://clickhouse.com/docs/en/sql-reference/statements/alter/ttl#materialize-ttl)
