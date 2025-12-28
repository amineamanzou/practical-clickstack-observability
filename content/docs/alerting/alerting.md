---
title: "Alerting"
weight: 1
description: "A guide to configuring and managing alerts using Webhooks, Saved Searches, and Charts."
---

This note outlines the workflow for setting up automated alerts to monitor system health, covering the configuration of notification channels, data sources, and trigger conditions.

## Alerting Workflow

**Question:** What are the required steps to configure and trigger alerts based on observability data?

**Answer Principle:** The alerting workflow involves three key phases: configuring a notification destination (**Webhook**), defining a data source (via **Saved Search** or **Chart**), and creating the **Alert** logic by setting a specific threshold and linking it to the webhook.

**Explanation:**
The alerting process is structured into the following sequential steps:

1. **Create a Webhook (Destination)**
    Before defining an alert, you must establish where notifications will be sent.
    * Navigate to the integrations or team settings.
    * **Service Type:** Choose the platform (e.g., *Slack*, *Generic*, *Incident.io*).
    * **Configuration:** Input the **Webhook Name** and **Webhook URL**.
    * *Example:* A webhook named "Post to @training-clickstack-alerts".

2. **Define the Data Source**
    Alerts must be attached to a specific view of your data.
    * **Option A: Saved Search (Log-based):**
        * Go to the Search page.
        * Define a filter (e.g., `ServiceName: "docs-loader"`).
        * Save the search configuration.
    * **Option B: Chart (Metric-based):**
        * Create or use an existing Dashboard.
        * Create a Chart (e.g., *Count of Events* where `StatusCode: "Error"`).
        * Save the chart.

3. **Create the Alert (Trigger Logic)**
    Once the source is defined, configure the condition that triggers the notification.
    * **Threshold:** Set the operator and value.
        * *Example:* Trigger when values are **Below (<) 100** lines within **1 minute**.
    * **Notification:** Select the previously configured Webhook to receive the alert.

4. **Management and Response**
    * **Getting Alerted:** When the condition is met, a notification is sent (e.g., to Slack) with the relevant time range and log lines. A follow-up notification is sent when the issue is resolved.
    * **Managing Alerts:** The Alerts dashboard provides an overview of all alerts, showing their current status as either **Triggered** (active issue) or **OK** (resolved).

**Reference:**

* Title: [Alerting in ClickStack | ClickHouse Docs](https://clickhouse.com/docs/use-cases/observability/clickstack/alerts)
