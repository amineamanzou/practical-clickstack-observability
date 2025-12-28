---
title: "Alerting Strategy: Search vs. Chart"
weight: 2
description: "A strategic comparison of Search-based and Chart-based alerting mechanisms to guide proper usage."
---

While the ClickStack UI allows alerting from both the Search explorer and Dashboard charts, choosing the right source is critical for effective observability.

## Search vs. Chart Alerts

**Question:** What is the difference between Search-based and Chart-based alerts, and when should I use each?

**Answer Principle:** Use **Search Alerts** to monitor the *frequency* of discrete events (e.g., specific log errors), and **Chart Alerts** to monitor *aggregated metrics* and trends (e.g., latency percentiles or failure rates).

**Explanation:**
The two alerting engines operate on different processing logic:

1. **Search-Based Alerts (Log & Event Focused)**
    * **Context:** Created directly from the Search page.
    * **Mechanism:** Counts the number of raw rows that match a specific filter (Lucene or SQL) within a time window.
    * **Key Capability:** **Dynamic Grouping**. You can group by a field (e.g., `ServiceName`), allowing a single alert rule to create separate monitors for every unique service found.
    * *Best Use Case:* "Trigger if the phrase 'Database Connection Failed' appears more than 0 times."

2. **Chart-Based Alerts (Metric Focused)**
    * **Context:** Created from a Chart within a Dashboard.
    * **Mechanism:** Evaluates the result of a SQL aggregation or calculation.
    * **Key Capability:** **Advanced Math**. Because it uses the chart's underlying SQL query, you can alert on derived values like ratios, averages, or quantiles, rather than just raw counts.
    * *Best Use Case:* "Trigger if the 99th percentile (P99) latency exceeds 500ms."

**Reference:**

* Title: [Alert Types in ClickStack | ClickHouse Docs](https://clickhouse.com/docs/use-cases/observability/clickstack/alerts)
