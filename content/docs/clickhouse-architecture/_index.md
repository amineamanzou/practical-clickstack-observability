---
title: "Fundamentals of ClickHouse Architecture"
weight: 1
description: "Explore the core concepts of ClickHouse Architecture, including partitioning, parts, granules, ingestion mechanics and background merges."
---

This section covers the core concepts of ClickHouse Architecture, including partitioning, parts, granules, ingestion mechanics, and background merges.

## Fundamentals of ClickHouse Architecture

{{% cards %}}
  {{% card title="ClickHouse Table Engines and Architecture" description="Overview of ClickHouse logical structure, database grouping, and the role of Table Engines." link="table-engine-and-architecture" icon="book-open" %}}
  {{% card title="Data Parts & Immutability" description="Understanding how ClickHouse handles inserts, data parts, and background merges." link="data-parts-and-immutability" icon="book-open" %}}
  {{% card title="Granules and Sparse Indexing" description="The mechanism behind ClickHouse's fast query performance using sparse primary indexes." link="granules-sparse-indexing" icon="book-open" %}}
  {{% card title="Optimizing Primary Keys" description="Best practices for choosing and ordering columns in a ClickHouse Primary Key." link="primary-key-selection" icon="book-open" %}}
  {{% card title="Table Partitioning Strategy" description="Understanding when to use partitioning versus primary keys." link="partitioning-strategies" icon="book-open" %}}
  {{% card title="Data Ingestion Strategies" description="Techniques to solve the 'too many small parts' problem during ingestion." link="ingestion-batching-vs-async" icon="book-open" %}}
  {{% card title="Atomic Table Operations" description="Advanced commands for managing table structure and aliases." link="special-table-operation" icon="book-open" %}}
  {{% card title="ClickStack Observability Architecture" description="The architectural pattern of using ClickHouse with OpenTelemetry for logs and traces." link="clickstack-observability-architecture" icon="book-open" %}}
  {{% card title="Secondary Indexing Strategies" description="Architectural patterns for optimizing queries that do not match the Primary Key." link="secondary-indexing-strategies" icon="book-open" %}}
  {{% card title="Ingestion Risks: Partitioning vs. Batching" description="Understanding how granular partitioning can negate the benefits of batching during ingestion." link="ingestion-mechanics" icon="book-open" %}}
  {{% card title="MergeTree Data Part Structure" description="Detailed analysis of how ClickHouse persists data into immutable parts, the internal file structure of Wide vs. Compact parts, and the role of the primary key index." link="mergetree-data-part" icon="book-open" %}}
  {{% card title="Partitioning Logic: High Cardinality Risks" description="Evaluation of partitioning keys, the distinction between Primary Keys and Partition Keys, and the catastrophic performance impact of high-cardinality partitioning (e.g., Timestamp)." link="partitioning-logic-cadinality" icon="book-open" %}}
{{% /cards %}}
