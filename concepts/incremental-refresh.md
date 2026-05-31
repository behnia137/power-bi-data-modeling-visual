# Incremental Refresh

## ELI5

Imagine your report pulls 3 years of sales data. Every morning it refreshes. Without incremental refresh: it re-downloads and re-processes all 3 years every single time. With incremental refresh: it only downloads yesterday's new data. The old data is already there — untouched.

## Visual

```mermaid
flowchart TD
    A[Full Date Range\n2022 - 2024] --> B{Incremental Refresh Policy}
    B --> C[Archive Window\n2022 - Nov 2024\nStored, not refreshed]
    B --> D[Refresh Window\nLast 2 months\nRe-queried every refresh]
    D --> E[Source DB runs:\nWHERE Date >= 2024-10-01]

    style B fill:#0078d4,color:#fff
    style C fill:#dbeafe,stroke:#3b82f6,color:#1f2937
    style D fill:#dcfce7,stroke:#22c55e,color:#1f2937
    style E fill:#fef3c7,stroke:#f59e0b,color:#1f2937
```

## How it works in practice

**Requirements:**
1. Power BI Premium, Premium Per User, or Fabric capacity (not Pro)
2. Query folding must work on the date/time column used for the policy
3. The table must have a date or datetime column to partition on

**Setup steps:**
1. In Power Query, create two parameters: `RangeStart` (DateTime) and `RangeEnd` (DateTime)
2. Filter your date column: `>= RangeStart AND < RangeEnd`
3. Power BI replaces these at refresh time with the actual window boundaries
4. In the dataset settings, define: Archive = 3 years, Refresh = 2 months

```powerquery
// Power Query filter — must use these exact parameter names
= Table.SelectRows(Source, each [OrderDate] >= RangeStart and [OrderDate] < RangeEnd)
```

**What happens at refresh:**
- Power BI partitions the table by period (month or day)
- Archive partitions are kept as-is
- Only refresh-window partitions are re-queried
- New data lands in the latest partition

**Key facts:**
- If query folding breaks, the entire date range is pulled — no error, just silent full-refresh behavior
- `Detect data changes` option lets you skip refreshing a partition if a "last updated" column hasn't changed — major performance win for append-only tables
- Historical partitions can be set to `Import` while the refresh window uses `DirectQuery` (hybrid mode)
- After publishing, incremental refresh policy cannot be changed without re-publishing the full dataset
- Use `XMLA endpoint` (Premium feature) to inspect and manage partitions directly
