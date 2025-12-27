---
title: "Query Acceleration Techniques"
weight: 3
description: "Explore the techniques for accelerating query performance in ClickHouse, including AggregatingMergeTree, Materialized Views, and Dynamic Filtering."
---

This section covers the techniques for accelerating query performance in ClickHouse, including AggregatingMergeTree, Materialized Views, and Dynamic Filtering.

## Query Acceleration Techniques

{{% cards %}}
  {{% card title="View Architectures: Standard, Refreshable, and Incremental" description="Comparing Standard Views, Refreshable Materialized Views, and Incremental Materialized Views." link="view-architectures" icon="book-open" %}}
  {{% card title="Materialized Views (In-Database ETL)" description="The 'Trigger-like' behavior of Materialized Views and their role as continuous ingestion pipelines." link="incremental-materialized-view" icon="book-open" %}}
  {{% card title="In-Database ETL Patterns" description="Using Materialized Views to implement Filtering, Routing, and Transformation." link="etl-materialized-view" icon="book-open" %}}
  {{% card title="The Average of Averages Problem" description="Why standard aggregation functions fail in pre-aggregated tables and the need for AggregatingMergeTree." link="average-of-average" icon="book-open" %}}
  {{% card title="Implementing Aggregations: -State and -Merge" description="The 3-step syntax for implementing incremental aggregations using combinator functions." link="implementing-aggregations" icon="book-open" %}}
  {{% card title="Dynamic Filtering in Materialized Views" description="Using subqueries and dictionary lookups within a Materialized View to filter data based on external lists." link="dynamic-filtering-lookups" icon="book-open" %}}
  {{% card title="Optimization: SimpleAggregateFunction" description="A specialized data type for optimizing storage and query simplicity for aggregations like Sum, Max, and Min." link="optimization-simpleaggregatefunction" icon="book-open" %}}
  {{% card title="Mechanics of Query Acceleration" description="Understanding the physical reason why AggregatingMergeTree provides exponential performance gains." link="mechanics-query-acceleration" icon="book-open" %}}
{{% /cards %}}
