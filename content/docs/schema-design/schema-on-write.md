---
title: "Computed Columns: Default, Materialized, Ephemeral"
weight: 5
description: "Techniques for performing ETL transformations directly within the table schema."
---

This note explains specific column types used to transform data during the ingestion process, reducing the need for external ETL.

## Computed Column Types

**Question:** How can you extract a value from a raw string at insert time without storing the raw data, and what is the role of `EPHEMERAL` columns?

**Answer Principle:** Use **`EPHEMERAL`** columns to accept raw input (placeholder, not stored). Use **`MATERIALIZED`** columns to compute and store a result derived from the ephemeral input. Use **`DEFAULT`** to provide values only when input is missing.

**Explanation:**

1. **`DEFAULT`:**
    * Computed *only* if the insert query creates a row without providing this column.
    * Example: `created_at DateTime DEFAULT now()`.

2. **`MATERIALIZED`:**
    * Computed *always* at insert time.
    * Physically stored on disk (can be indexed/compressed).
    * **Constraint:** You cannot insert explicitly into a materialized column.
    * *Note:* Not returned in `SELECT *` (must be requested explicitly).

3. **`EPHEMERAL`:**
    * Input-only column.
    * **Not stored** on disk. Not queryable.
    * **Use Case:** Ingest a raw JSON blob into an `EPHEMERAL` column, use `MATERIALIZED` columns to extract 5 specific fields to disk, and discard the blob.

**Reference:**

* Title: [Default Expressions](https://clickhouse.com/docs/en/sql-reference/statements/create/table#default_values)
