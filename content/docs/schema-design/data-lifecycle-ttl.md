---
title: "Data Lifecycle (TTL)"
weight: 6
description: "Managing data expiration and storage tiering using TTL clauses."
---

This note explains the Time-To-Live (TTL) mechanism for automating data deletion and movement.

## TTL (Time To Live)

**Question:** How do you configure ClickHouse to automatically delete old data or move it to cheaper storage (S3/HDD)?

**Answer Principle:** Use the **TTL** clause in the table definition. TTL triggers background operations to **Delete** rows, **Reset** columns, or **Move** data parts to different storage volumes (Tiering).

**Explanation:**

1. **Row Expiration:**
    * Syntax: `TTL timestamp + INTERVAL 1 MONTH`.
    * Action: Deletes the row when the condition is met.

2. **Storage Tiering (Hot/Cold):**
    * Syntax: `TTL timestamp + INTERVAL 7 DAY TO VOLUME 'cold', timestamp + INTERVAL 1 MONTH DELETE`.
    * Action: Moves data parts from fast disk (NVMe) to slow disk/S3 (Cold) after 7 days, then deletes them after 1 month.

3. **Execution:**
    * TTL operations happen "lazily" during background merges.
    * You can control the frequency via `merge_with_ttl_timeout` (default 4 hours).

**Reference:**

* Title: [TTL](https://clickhouse.com/docs/en/engines/table-engines/mergetree-family/mergetree#table_engine-mergetree-ttl)
