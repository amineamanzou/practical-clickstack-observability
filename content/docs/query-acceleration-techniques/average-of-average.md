---
title: "The Average of Averages Problem"
weight: 4
description: "Why standard aggregation functions fail in pre-aggregated tables and the need for AggregatingMergeTree."
---

This note explains the mathematical reason why specific table engines are required for roll-up aggregations.

## Storing Intermediate State

**Question:** Why can't you simply store the result of `avg(latency)` in a Materialized View target table and query it later?

**Answer Principle:** You cannot mathematically average a set of pre-calculated averages (the "Average of Averages" fallacy). To re-aggregate data (e.g., rolling up minutes into hours), you must store the **Intermediate State** (the sum and the count) rather than the final scalar value. `AggregatingMergeTree` is designed to store this state.

**Explanation:**

1. **The Problem:**
    * Batch A: Values `[10, 20]`. Avg = 15. (Count 2)
    * Batch B: Value `[100]`. Avg = 100. (Count 1)
    * *Wrong:* `avg(15, 100) = 57.5`.
    * *Correct:* `sum(10+20+100) / count(2+1) = 43.3`.

2. **The Solution (`AggregatingMergeTree`):**
    * This engine does not store the value `15`.
    * It stores a binary state object: `{sum: 30, count: 2}`.
    * When queried, ClickHouse merges the states: `{sum: 30+100, count: 2+1}` to produce the correct average.

3. **Efficiency:**
    * ClickHouse merges rows with the same Primary Key in the background, combining their states into a single row. This dramatically reduces storage size compared to raw logs.

**Reference:**

* Title: [AggregatingMergeTree Engine](https://clickhouse.com/docs/en/engines/table-engines/mergetree-family/aggregatingmergetree)
