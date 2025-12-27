---
title: "String Optimization: LowCardinality"
weight: 2
description: "Using the LowCardinality data type to optimize storage and performance for repetitive strings."
---

This note explains `LowCardinality`, a specific ClickHouse optimization for string columns with low uniqueness.

## LowCardinality Data Type

**Question:** When should you use `LowCardinality(String)` instead of a standard `String`?

**Answer Principle:** Use `LowCardinality` when a string column has a **small number of unique values** (typically fewer than 10,000). It utilizes dictionary encoding to store integers instead of strings, reducing storage size and accelerating queries.

**Explanation:**

1. **Mechanism:**
    * ClickHouse creates a local dictionary of the unique string values.
    * The column physically stores integer keys pointing to that dictionary.
    * At query time, operations (filters, grouping) run on the integers (fast) rather than strings (slow).

2. **Usage:**
    * **Ideal Candidates:** `Environment` (Prod/Dev), `LogLevel` (Info/Error), `Region`, `Status`.
    * **Avoid For:** High cardinality data like `UUID`, `TraceID`, or `MessageBody`, where the dictionary overhead outweighs the benefits.

3. **Behavior:**
    * It behaves transparently as a `String` to the user. You do not need to manage the dictionary manually (unlike an `Enum`).

**Reference:**

* Title: [LowCardinality Data Type](https://clickhouse.com/docs/en/sql-reference/data-types/lowcardinality)
