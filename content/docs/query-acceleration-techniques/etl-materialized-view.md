---
title: "In-Database ETL Patterns"
weight: 3
description: "Using Materialized Views to implement Filtering, Routing, and Transformation."
---

This note demonstrates how to use MVs to clean, split, and structure data during ingestion, effectively using ClickHouse as an ETL engine.

## Filtering, Routing, and Transformation

**Question:** How can you use ClickHouse to filter unwanted logs, split streams into different tables, or extract columns from strings?

**Answer Principle:** You can chain Materialized Views to perform ETL. Use `WHERE` clauses for **Filtering**, create multiple MVs on one source for **Routing**, and use SQL functions (like Regex) for **Transformation**.

**Explanation:**

1. **Filtering (Data Cleaning):**
    * Use a `WHERE` clause in the MV to exclude rows.
    * *Example:* `WHERE ip != '10.20.30.40'` (Drops internal traffic before it hits the target table).

2. **Routing (Splitting Streams):**
    * Create multiple MVs attached to the same `raw_logs` source.
    * *MV A:* `WHERE log_level = 'ERROR'` -> Writes to `error_logs` table (Long retention).
    * *MV B:* `WHERE log_level = 'INFO'` -> Writes to `info_logs` table (Short retention).

3. **Transformation (Structure Extraction):**
    * Use functions to parse unstructured data.
    * *Example:* `extract(message, 'ip=([0-9.]+)')` to pull an IP address from a log message into a strongly typed column.

**Reference:**

* Title: [Materialized Views for ETL](https://clickhouse.com/docs/en/guides/developer/casestudies/materialized-views-etl)
