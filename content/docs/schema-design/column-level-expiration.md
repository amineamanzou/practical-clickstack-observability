---
title: "Column-Level Expiration"
weight: 9
description: "Techniques for deleting specific columns from a row while preserving the rest of the record."
---

This note details the capability to expire data "vertically" (removing specific columns) rather than "horizontally" (deleting entire rows).

## Column TTL

**Question:** How can you reduce storage for old data by deleting only heavy columns (like `payload` or `message_body`) while keeping summary columns (like `status` and `timestamp`) searchable?

**Answer Principle:** Use **Column TTL**. You can define a TTL on individual columns within the table schema. When the TTL is reached, the column's values are reset to their default, or if all values in a part are expired, the column file is physically deleted.

**Explanation:**

1. **Syntax:**

    ```sql
    ALTER TABLE logs 
    MODIFY COLUMN message String TTL timestamp + INTERVAL 7 DAY;
    ```

2. **Behavior:**
    * **Partial Expiration (Reset):** When the 7 days pass, the `message` column for those rows is reset to its default value (e.g., empty string). This allows the column to compress extremely well (almost zero space).
    * **Full Deletion:** If *every* row in a specific data part has an expired `message` column, ClickHouse deletes the physical `.bin` and `.mrk` files for that column from the disk, reclaiming 100% of the space.

**Reference:**

* Title: [Column TTL](https://clickhouse.com/docs/en/engines/table-engines/mergetree-family/mergetree#column-ttl)
