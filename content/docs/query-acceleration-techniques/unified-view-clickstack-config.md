---
title: "Unified Views & ClickStack Configuration"
weight: 12
description: "Using the Merge engine and Application settings to query across historical and active tables."
---

After an atomic table exchange, your data is split: historical data resides in the "old/archive" table, and new data flows into the "new/active" table.

## Unified Data Access

**Question:** How can I query and visualize data distributed across both legacy and new table structures simultaneously?

**Answer Principle:** Create a virtual table using the `Merge` engine to aggregate data from all tables matching a regex, then configure the application (ClickStack) to read from this combined view.

**Explanation:**

1. **Database Level (The Merge Engine):**
    Create a virtual table that proxies queries to all tables matching a pattern.

    ```sql
    -- Reads from 'otel_logs' (New) AND 'otel_logs_original_primary_key' (Old)
    CREATE TABLE combined_logs ENGINE = Merge('default', 'otel_logs*');
    ```

2. **Application Level (ClickStack UI):**
    To visualize this unified data in the dashboard:
    * Navigate to **Team Settings** (left-hand menu).
    * Update the **Logs** table source to `combined_logs`.
    * Click **Save Source**.

    The application will now transparently display rows from both the active partition and the archived partition.

**Reference:**

* Title: [Merge Table Engine | ClickHouse Docs](https://clickhouse.com/docs/en/engines/table-engines/special/merge)
