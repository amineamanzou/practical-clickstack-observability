---
title: "Data Ingestion Strategies"
weight: 6
description: "Techniques to solve the 'too many small parts' problem during ingestion."
---

This note explains how to handle high-frequency inserts without crashing the database due to part proliferation.

## Batching vs. Async Inserts

**Question:** How do you handle an application sending thousands of individual insert requests per second to ClickHouse?

**Answer Principle:** You must buffer data to create larger parts. This can be done via **Client-side Batching** (using collectors or code) or **Server-side Batching** (using `async_insert`).

**Explanation:**

1. **The Problem:**
    * Raw inserts (1 row at a time) create 1 part per row.
    * Merge process cannot keep up.
    * Result: `Too many parts` error.

2. **Strategy 1: External Batching (Preferred)**
    * Use tools like **OpenTelemetry (OTel) Collector**.
    * Configure `batch` processor (e.g., `timeout: 10s`, `send_batch_size: 100000`).
    * Benefits: Better compression, fewer network connections, lower load on ClickHouse.

3. **Strategy 2: Server-Side Async Insert**
    * If client cannot batch, enable `async_insert = 1` in ClickHouse settings.
    * **Mechanism:** ClickHouse accepts the request but buffers it in an in-memory buffer (`Buffer`). It flushes to disk only when the buffer is full or a timeout is reached.
    * **Trade-off:** Clients might wait for the flush (default) or fire-and-forget (`wait_for_async_insert = 0`), posing a slight risk of data loss on crash.

**Reference:**

* Title: [Async Inserts](https://clickhouse.com/docs/en/optimize/asynchronous-inserts)
