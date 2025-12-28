---
title: "ClickHouse Codec Selection: LZ4 vs. ZSTD"
weight: 12
description: "A comparison of LZ4 and ZSTD codecs in ClickHouse, focusing on the trade-off between ingestion speed and storage efficiency, with implementation examples."
---

This note captures the findings from the `otel_logs` optimization lab, specifically regarding column-level compression settings.

## ClickHouse Codec Selection: LZ4 vs. ZSTD

**Question:** What are the performance trade-offs between LZ4 and ZSTD codecs in ClickHouse, and how are they implemented and benchmarked?

**Answer Principle:** **LZ4** is optimized for speed, offering faster ingestion and lower CPU overhead, making it suitable for high-throughput scenarios. **ZSTD** is optimized for storage efficiency, providing higher compression ratios at the cost of increased CPU usage and slightly slower insertion speeds.

**Explanation:**

In ClickHouse, codecs can be applied at the column level to optimize for specific hardware constraints (storage vs. CPU) or data lifecycles.

1. The Trade-off Hierarchy

    * **ZSTD (Zstandard):**
        * **Pros:** High compression ratio (uses less disk space).
        * **Cons:** Higher CPU usage during compression/decompression; slower `INSERT` performance.
        * **Use Case:** General storage, historical data, or when disk space is the primary cost constraint.
    * **LZ4:**
        * **Pros:** Extremely fast compression/decompression speed; low CPU overhead.
        * **Cons:** Lower compression ratio (uses more disk space).
        * **Use Case:** High-speed ingestion (e.g., real-time logs), hot data, or when CPU is the bottleneck.

    ```mermaid
    graph TD
        A[Data Ingestion Strategy] --> B{Optimization Goal?}
        B -->|Maximize Throughput| C[LZ4]
        B -->|Minimize Disk Usage| D[ZSTD]
        C --> E[Fast Insert / Higher Storage Cost]
        D --> F[Slower Insert / Lower Storage Cost]
    ```

2. Implementation Syntax

    Codecs are defined in the DDL. You can combine codecs, such as applying Delta encoding before compression for time-series data.

    Modifying an existing column (LZ4):

    ```sql
    ALTER TABLE otel_logs 
    MODIFY COLUMN Timestamp DateTime64(9) CODEC(Delta, LZ4);
    ```

    Defining columns in a new table (ZSTD vs LZ4):

    ```sql
    -- LZ4 Example
    `Timestamp` DateTime64(9) CODEC(Delta(8), LZ4),
    `TraceId` String CODEC(LZ4),

    -- ZSTD Example
    `Timestamp` DateTime64(9) CODEC(Delta(8), ZSTD(1)),
    `TraceId` String CODEC(ZSTD(1)),
    ```

3. Benchmarking Storage Efficiency

    To empirically measure the difference in compression between two tables (e.g., test_lz4_codec vs. test_zstd_codec) after loading identical datasets, query the system.parts table.

    ```sql
    SELECT
        table,
        name,
        formatReadableQuantity(data_uncompressed_bytes) AS uncompressed_size,
        formatReadableQuantity(data_compressed_bytes) AS compressed_size
    FROM system.parts
    WHERE (`table` LIKE 'test_%_codec') AND (active = 1);
    ```

**References:**

* [ClickHouse Documentation: Column Compression Codecs](https://clickhouse.com/docs/sql-reference/statements/create/table#column_compression_codec)
