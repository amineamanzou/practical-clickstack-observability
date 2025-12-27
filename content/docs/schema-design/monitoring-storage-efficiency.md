---
title: "Monitoring Storage Efficiency"
weight: 10
description: "How to inspect disk usage and compression ratios at the column level."
---

This note provides the SQL query patterns to inspect the physical footprint of your schema and verify if your Codec and Data Type choices are effective.

## Analyzing Column Storage

**Question:** How do you calculate the exact disk usage and compression ratio of a specific column?

**Answer Principle:** Query the **`system.columns`** table. It contains detailed metadata for every column, specifically `data_compressed_bytes` (size on disk) and `data_uncompressed_bytes` (raw size/memory usage).

**Explanation:**

1. **The Query:**
    Use this query to audit your schema efficiency:

    ```sql
    SELECT 
        name,
        formatReadableSize(data_compressed_bytes) as compressed,
        formatReadableSize(data_uncompressed_bytes) as uncompressed,
        round(data_uncompressed_bytes / data_compressed_bytes, 2) as ratio
    FROM system.columns
    WHERE table = 'my_table'
    ORDER BY data_compressed_bytes DESC;
    ```

2. **Interpretation:**
    * **High Ratio (e.g., 20.0+):** Excellent compression. The data is likely repetitive or monotonic (e.g., Time with `Delta`, or Status with `LowCardinality`).
    * **Low Ratio (e.g., ~1.0):** Poor compression. The data likely has high entropy (e.g., random strings, UUIDs, encrypted hashes).

3. **Action:**
    * Identify the top columns by `compressed` size and check if their `ratio` is low. If so, apply a better Codec (like `ZSTD`) or specialized Data Type.

**Reference:**

* Title: [System Columns Table](https://clickhouse.com/docs/en/operations/system-tables/columns)
