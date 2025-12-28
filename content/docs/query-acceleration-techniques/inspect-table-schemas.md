---
title: "Inspecting Table Schemas"
weight: 9
description: "How to retrieve reproducible SQL DDL statements for existing ClickHouse tables."
---

When performing schema migrations, you often need the exact SQL definition of an existing table to use as a template for a new one.

## SHOW CREATE TABLE

**Question:** How do I extract the clean `CREATE` statement for an existing table to use as a base for refactoring?

**Answer Principle:** Use the `SHOW CREATE TABLE` command with the `RawWithNamesAndTypes` format to obtain a clean, strictly typed SQL statement.

**Explanation:**
Standard `SHOW CREATE` commands often output formatting that is optimized for readability or includes engine-specific metadata. To get a "clean" version that is ready to be copied, modified, and executed:

1. **List Tables:**

    ```sql
    SHOW TABLES;
    ```

2. **Get Definition:**

    ```sql
    SHOW CREATE TABLE otel_logs FORMAT RawWithNamesAndTypes;
    ```

    * **Why this format?** It strips unnecessary decorators and provides strict types, making it safe to reuse for creating new tables.

**Reference:**

* Title: [SHOW CREATE TABLE | ClickHouse Docs](https://clickhouse.com/docs/en/sql-reference/statements/show-create-table)
