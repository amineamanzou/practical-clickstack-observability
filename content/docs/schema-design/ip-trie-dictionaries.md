---
title: "ClickHouse IP Trie Dictionaries"
weight: 14
description: "Using IP Trie dictionaries for low-latency IP address lookups and geolocation."
---

ClickHouse provides specialized dictionary layouts to handle IP networking data efficiently.

## IP Trie Dictionary Layout

**Question:** How can we perform low-latency lookups for IP addresses to retrieve metadata (like geolocation) based on network prefixes?

**Answer Principle:** Use a ClickHouse Dictionary with the `LAYOUT(IP_TRIE)` configuration, which maps network prefixes (CIDR) to values.

**Explanation:**
Standard dictionaries hash keys, but IP addresses often require range matching. The `IP_TRIE` layout is optimized for mapping network prefixes to values, such as mapping an IP address to a City or Country.

To use this, the source data must contain a column representing the IP range in CIDR notation (e.g., `192.168.1.0/24`). The dictionary resides in RAM, allowing for extremely fast lookups during query execution.

**Example Configuration:**

```sql
CREATE DICTIONARY ip_trie (
   cidr         String,
   city         String,
   country_code String
)
PRIMARY KEY cidr
SOURCE(CLICKHOUSE(TABLE 'geoip')) -- Source table must have CIDR strings
LAYOUT(IP_TRIE)
LIFETIME(3600);
```

**Usage:**

```sql
SELECT dictGet(
    'ip_trie',
    ('city', 'country_code'),
    toIPv4('8.8.8.8')
) AS ip_details;
```

**References:**

* [ClickHouse Documentation: IP Trie Dictionary](https://clickhouse.com/docs/sql-reference/dictionaries#ip_trie)
