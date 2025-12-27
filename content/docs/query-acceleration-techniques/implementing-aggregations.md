---
title: "Implementing Aggregations: -State and -Merge"
weight: 5
description: "The 3-step syntax for implementing incremental aggregations using combinator functions."
---

This note details the specific SQL syntax required to implement `AggregatingMergeTree`.

## The State-Merge Workflow

**Question:** What are the three steps (and corresponding function suffixes) required to implement a correct roll-up?

**Answer Principle:**

1. **Define:** The target table uses `AggregateFunction` data types.
2. **Insert:** The Materialized View uses **`-State`** combinator functions (e.g., `avgState`) to produce binary states.
3. **Query:** The SELECT query uses **`-Merge`** combinator functions (e.g., `avgMerge`) to calculate the final result.

**Explanation:**

1. **Step 1: Target Table:**

    ```sql
    CREATE TABLE stats (
        avg_lat AggregateFunction(avg, Float64),
        max_lat SimpleAggregateFunction(max, Float64)
    ) ENGINE = AggregatingMergeTree ...
    ```

2. **Step 2: Population (MV):**

    ```sql
    SELECT
        avgState(latency) as avg_lat,
        maxSimpleState(latency) as max_lat
    FROM raw_logs ...
    ```

    * This transforms raw numbers into the binary state format.

3. **Step 3: Querying:**

    ```sql
    SELECT
        avgMerge(avg_lat),
        max(max_lat) -- SimpleAggregateFunction allows using standard functions
    FROM stats ...
    ```

    * This aggregates the intermediate states into a final readable number.
    * *Optimization:* Use `SimpleAggregateFunction` for simple metrics (like `max`, `sum`) to allow usage of standard SQL functions without the `-Merge` suffix in some contexts.

**Reference:**

* Title: [Aggregate Function Combinators](https://clickhouse.com/docs/en/sql-reference/aggregate-functions/combinators)
