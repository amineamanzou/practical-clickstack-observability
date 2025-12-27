---
title: "Compression and Codecs"
weight: 4
description: "Understanding the layered approach to data reduction using Codecs and Compression Algorithms."
---

This note details how ClickHouse minimizes disk I/O through encoding and compression.

## Codecs vs. Compression

**Question:** What is the difference between a **Codec** and a **Compression Algorithm** in ClickHouse?

**Answer Principle:** **Codecs** (e.g., `Delta`, `DoubleDelta`) transform the data logic to reduce entropy (e.g., storing the difference between timestamps). **Compression Algorithms** (e.g., `LZ4`, `ZSTD`) perform generic byte-level compression. They are chained: Codec first, then Compression.

**Explanation:**

1. **Codecs (Logical Transformation):**
    * **`Delta(N)`:** Stores the difference between consecutive values.
        * *Best for:* Monotonic time-series data (`Timestamp`) or counters.
    * **`DoubleDelta`:** Stores the difference of differences.
        * *Best for:* Data changing at a constant rate (e.g., exactly 1 sample per second).

2. **Compression (Byte Reduction):**
    * **`LZ4`:** (Default) Fast decompression, moderate ratio. Good for hot data.
    * **`ZSTD`:** Higher ratio, slower. Good for logs and cold data.

3. **Example:**
    * `CODEC(Delta(8), ZSTD(1))` applied to a `DateTime64` column significantly reduces storage by removing the repetition in the high bits of the timestamp.

**Reference:**

* Title: [Column Compression Codecs](https://clickhouse.com/docs/en/sql-reference/statements/create/table#codecs)
