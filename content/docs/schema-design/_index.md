---
title: "Schema Design"
weight: 2
description: "Explore the concepts of schema design in ClickHouse, including the use of AggregateFunction and SimpleAggregateFunction."
---

This section covers the concepts of schema design in ClickHouse, including data types, compression, ETL transformations, and data lifecycle management.

## Schema Design

{{% cards %}}
  {{% card title="Map vs. JSON" description="Understanding the trade-offs between dynamic JSON columns and the structured Map data type." link="map-vs-json" icon="book-open" %}}
  {{% card title="String Optimization: LowCardinality" description="Using the LowCardinality data type to optimize storage and performance for repetitive strings." link="string-optimization-low-cardinality" icon="book-open" %}}
  {{% card title="Adaptive Index Granularity" description="How ClickHouse manages index size and performance through automatic granule adjustment." link="adaptive-index-granularity" icon="book-open" %}}
  {{% card title="Compression and Codecs" description="Understanding the layered approach to data reduction using Codecs and Compression Algorithms." link="compression-codecs" icon="book-open" %}}
  {{% card title="Computed Columns: Default, Materialized, Ephemeral" description="Techniques for performing ETL transformations directly within the table schema." link="schema-on-write" icon="book-open" %}}
  {{% card title="Data Lifecycle (TTL)" description="Managing data expiration and storage tiering using TTL clauses." link="data-lifecycle-ttl" icon="book-open" %}}
  {{% card title="TTL Efficiency: Part Dropping" description="Optimizing data deletion performance by dropping whole parts instead of rewriting them." link="ttl-efficiency" icon="book-open" %}}
  {{% card title="Operational TTL Management" description="How to manage, tune, and force the application of Time-To-Live policies on existing data." link="operational-ttl-management" icon="book-open" %}}
  {{% card title="Column-Level Expiration" description="Techniques for deleting specific columns from a row while preserving the rest of the record." link="column-level-expiration" icon="book-open" %}}
  {{% card title="Monitoring Storage Efficiency" description="How to inspect disk usage and compression ratios at the column level." link="monitoring-storage-efficiency" icon="book-open" %}}
  {{% card title="Materialized Columns" description="Using materialized columns to simplify syntax and optimize access for nested data structures in ClickHouse." link="materialized-columns" icon="book-open" %}}
{{% /cards %}}
