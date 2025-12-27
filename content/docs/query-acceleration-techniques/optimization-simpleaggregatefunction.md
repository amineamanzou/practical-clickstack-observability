---
title: "Optimization: SimpleAggregateFunction"
weight: 7
description: "A specialized data type for optimizing storage and query simplicity for aggregations like Sum, Max, and Min."
---

This note details a specific optimization for `AggregatingMergeTree` that allows using standard SQL syntax.

## SimpleAggregateFunction

**Question:** When defining an `AggregatingMergeTree`, why would you use `SimpleAggregateFunction` instead of `AggregateFunction`, and what are the benefits?

**Answer Principle:** `SimpleAggregateFunction` is used for aggregations where the "intermediate state" is identical to the final value (e.g., `sum`, `max`, `min`, `any`). It avoids the overhead of complex binary states and allows querying the column using standard SQL functions (e.g., `max(col)`) without needing the `-Merge` suffix combinators.

**Explanation:**

1. **The Mechanism:**
    * **Standard `AggregateFunction`:** Stores an opaque binary blob (e.g., `avg` stores sum and count). Requires `avgMerge` to read.
    * **`SimpleAggregateFunction`:** Stores the value directly (e.g., `max` stores the number). ClickHouse knows how to merge these values implicitly.

2. **Benefits:**
    * **Usability:** You can query it as a normal column.
        * *Schema:* `max_lat SimpleAggregateFunction(max, Float64)`
        * *Query:* `SELECT max(max_lat) FROM table` (No need for `maxMerge`).
    * **Compatibility:** Easier to integrate with BI tools (Tableau, Superset) that generate standard SQL.
    * **Storage:** Often compresses better than binary blobs.

**Reference:**

* Title: [SimpleAggregateFunction](https://clickhouse.com/docs/en/sql-reference/data-types/simpleaggregatefunction)
