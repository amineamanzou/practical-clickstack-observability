---
title: "Data Types and Time Precision"
weight: 1
description: "Understanding ClickHouse data types with a focus on DateTime precision and timezone handling."
---

This note details the fundamental data types in ClickHouse, highlighting the critical difference between second-level and sub-second time precision.

## DateTime vs. DateTime64

**Question:** What is the difference between `DateTime` and `DateTime64`, and how should timezones be handled?

**Answer Principle:** `DateTime` stores time with **second** resolution (4 bytes). `DateTime64` stores time with **sub-second** resolution (up to nanoseconds, 8 bytes). Timezones are stored as **metadata per column**, modifying how the stored integer is displayed/parsed, but not the value itself.

**Explanation:**

1. **Standard Types:**
    * **Integers:** `UInt8` to `UInt256` (Fixed length).
    * **Floats:** `Float32`, `Float64`, `Decimal`.
    * **Strings:** `String` (Variable length, replaces VARCHAR), `FixedString(N)` (Padded).

2. **Time Resolution:**
    * **`DateTime`:** Stores seconds (Unix timestamp). Range: 1970 to 2106.
    * **`DateTime64(precision)`:** Stores "ticks".
        * `precision=3`: Milliseconds.
        * `precision=9`: Nanoseconds.
    * *Use Case:* Always use `DateTime64` for logs requiring millisecond precision. Using `DateTime` will truncate the milliseconds.

3. **Querying Pitfall:**
    * When grouping by time (e.g., for a graph), avoid grouping by the raw `DateTime64` column as nearly every row will be unique.
    * Use truncation functions like `toStartOfMinute(Timestamp)` to create meaningful buckets.

**Reference:**

* Title: [Date and Time Types](https://clickhouse.com/docs/en/sql-reference/data-types/date-time)
