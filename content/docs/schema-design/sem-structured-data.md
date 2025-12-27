---
title: "Semi-Structured Data: Map vs JSON"
weight: 3
description: "Strategies for handling dynamic schemas using Map and JSON data types."
---

This note compares the two primary methods for storing dynamic or nested data structures in ClickHouse.

## Map vs. JSON

**Question:** How does ClickHouse handle dynamic schemas, and what is the trade-off between `Map` and `JSON` types?

**Answer Principle:** `Map(K,V)` is strongly typed and best for simple key-value pairs where keys vary but types are consistent. The `JSON` type handles complex, nested, mixed-type data and automatically optimizes storage by extracting frequent fields into dedicated columns ("Schema on Write").

**Explanation:**

1. **Map(Key, Value):**
    * **Structure:** Strict types, e.g., `Map(String, String)`.
    * **Access:** `col['key']`.
    * **Performance:** Linear scan of the map. Slower for retrieving a single key from a large map.

2. **JSON (Object):**
    * **Structure:** Flexible. Can store nested objects and varying types.
    * **Optimization:** ClickHouse analyzes the data during insertion.
        * **Frequent Paths:** Automatically promoted to "concrete" sub-columns (fast physical storage).
        * **Rare Paths:** Stored in a shared dynamic blob.
    * **Access:** Dot notation `col.key.subkey`.

3. **Recommendation:**
    * Use `JSON` for observability logs (e.g., OpenTelemetry attributes) to balance flexibility with columnar performance.

**Reference:**

* Title: [JSON Data Type](https://clickhouse.com/docs/en/sql-reference/data-types/json)
