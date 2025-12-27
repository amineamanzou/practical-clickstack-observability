---
title: "Secondary Indexing Strategies"
weight: 9
description: "Architectural patterns for optimizing queries that do not match the Primary Key."
---

This note details the specific options available when you need to filter data by a column that is not part of the table's physical sort order.

## Handling Multiple Query Patterns

**Question:** Since a table can only be physically sorted by one Primary Key (e.g., Time), how do you optimize queries that filter by a different high-cardinality column (e.g., TraceID)?

**Answer Principle:** You cannot efficiently index two conflicting sort orders in one physical table. You must create auxiliary structures. The main strategies are **Projections**, **Materialized Views**, **Skipping Indexes**, or **Secondary Tables**.

**Explanation:**

1. **Projections (Recommended):**
    * ClickHouse maintains a hidden, internal table sorted by a different key (e.g., `ORDER BY TraceID`).
    * **Pros:** Automatic query routing (the optimizer picks the fastest projection), atomic consistency.
    * **Cons:** Increases disk usage.

2. **Materialized Views (MV):**
    * An `INSERT` trigger creates a copy of the data in a completely separate table with a different `ENGINE` and `PRIMARY KEY`.
    * **Pros:** Highly flexible; allows for aggregation and filtering during the copy process.

3. **Skipping Indexes:**
    * Lightweight structures (Bloom Filters, Min/Max) stored alongside the data granules.
    * **Pros:** Low storage overhead.
    * **Cons:** They do not re-sort data, so they are less effective than Projections for high-cardinality point lookups.

4. **Double Writes:**
    * The application manually writes to two different tables. (Generally discouraged due to complexity).

**Reference:**

* Title: [Table Projections](https://clickhouse.com/docs/en/sql-reference/statements/alter/projection)
