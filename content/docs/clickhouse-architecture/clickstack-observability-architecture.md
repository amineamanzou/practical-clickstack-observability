---
title: "ClickStack Observability Architecture"
weight: 8
description: "The architectural pattern of using ClickHouse with OpenTelemetry for logs and traces."
---

This note outlines the "ClickStack" architecture, which integrates OpenTelemetry with ClickHouse to build a scalable observability backend.

## ClickStack Components

**Question:** What is the standard architecture for building a high-performance observability pipeline using ClickHouse?

**Answer Principle:** The "ClickStack" decouples data generation from storage. It uses the **OpenTelemetry (OTel) Collector** as a gateway to buffer and batch data before writing it to **ClickHouse**. A visualization layer (like HyperDX) queries ClickHouse to display the telemetry.

**Explanation:**

1. **Data Generation (SDKs/Agents):**
    * Applications (Go, Python, Rust) and Infrastructure (Kubernetes) emit logs, metrics, and traces using standard OTel SDKs.
    * This data is pushed to a central collector, not the database directly.

2. **The Gateway (OTel Collector):**
    * Acts as the ingestion buffer.
    * **Critical Role:** It converts constant streams of small events into large, compressed batches (e.g., 10 seconds or 100,000 rows).
    * This prevents "Write Amplification" in ClickHouse.

3. **Storage (ClickHouse):**
    * Receives optimized batches from the Collector.
    * Uses `MergeTree` tables, typically partitioned by `Day` or `Month` to facilitate Time-To-Live (TTL) data retention policies.

4. **Visualization:**
    * Compatible frontends query ClickHouse directly to render dashboards and search logs.

**Reference:**

* Title: [OpenTelemetry ClickHouse Exporter](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/clickhouseexporter)
