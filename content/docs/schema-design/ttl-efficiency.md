---
title: "TTL Efficiency: Part Dropping"
weight: 7
description: "Optimizing data deletion performance by dropping whole parts instead of rewriting them."
---

This note focuses on a specific configuration to make TTL operations significantly more efficient for time-series/log data.

## Efficient Deletion (`ttl_only_drop_parts`)

**Question:** Why is standard TTL resource-intensive, and how can you optimize it for high-volume logging tables?

**Answer Principle:** Standard TTL (`ttl_only_drop_parts = 0`) rewrites data parts to filter out specific expired rows, consuming heavy CPU and I/O. Enabling **`ttl_only_drop_parts = 1`** instructs ClickHouse to wait until *every* row in a part is expired, then simply delete the entire part folder.

**Explanation:**

1. **The Rewrite Cost:**
    * If a part spans 1 hour and your TTL is "7 days", eventually rows expire.
    * ClickHouse reads the part, removes expired rows, and writes a new part. This is a "Mutation".

2. **The Optimization:**
    * If you align your **Partition Key** (e.g., Day) with your **TTL** (e.g., Day boundaries), eventually entire parts contain only expired data.
    * With `ttl_only_drop_parts = 1`, ClickHouse drops the part.
    * **Cost:** Near zero resource usage.
    * **Trade-off:** Data might persist slightly longer than the exact TTL until the whole part expires.

**Reference:**

* Title: [TTL Settings](https://clickhouse.com/docs/en/engines/table-engines/mergetree-family/mergetree#ttl_only_drop_parts)
