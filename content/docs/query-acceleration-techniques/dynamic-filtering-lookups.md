---
title: "Dynamic Filtering in Materialized Views"
weight: 6
description: "Using subqueries and dictionary lookups within a Materialized View to filter data based on external lists."
---

This note explains how to implement dynamic exclusion lists (e.g., blocking specific IPs) inside an ingestion pipeline without changing application code.

## Ingestion-Time Lookups

**Question:** How can a Materialized View filter incoming logs based on a dynamic list of values (like blocked IPs) stored in a separate table?

**Answer Principle:** You can use **Subqueries** or **ClickHouse Dictionaries** inside the `WHERE` clause of the Materialized View's `SELECT` statement. The MV executes this lookup for every inserted block of data to decide which rows to include in the target table.

**Explanation:**

1. **The Scenario:**
    * You have a table `ip_table` containing a list of internal IP addresses.
    * You want your primary `my_logs` table to exclude these IPs automatically.

2. **The Implementation:**
    * Instead of hardcoding IPs in the `WHERE` clause, the MV queries the lookup table:

    ```sql
    CREATE MATERIALIZED VIEW logs_mv TO my_logs AS
    SELECT * FROM raw_logs
    WHERE NOT has(
        (SELECT groupArray(ip) FROM ip_table), -- The Lookup
        extracted_ip
    );
    ```

3. **Performance Consideration:**
    * **Subqueries:** Executed once per inserted block. Can be slow if the lookup table is large.
    * **Dictionaries:** For high-performance ingestion, use `dictGet` or the `IS IN` operator with a ClickHouse Dictionary, which is cached in memory.

**Reference:**

* Title: [Dictionaries for Lookups](https://clickhouse.com/docs/en/sql-reference/dictionaries)
