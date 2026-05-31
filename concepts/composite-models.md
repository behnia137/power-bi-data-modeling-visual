# 🧩 Composite Models

> **🧒 Explain Like I'm 5:** Mix Import tables and DirectQuery tables in the same report model.

## 🖼️ The Picture

```mermaid
flowchart LR
    subgraph Model["🧩 Composite Model"]
        Import1["📦 DimProduct\n(Import — fast)"]
        Import2["📅 DimDate\n(Import — fast)"]
        DQ["💰 FactSales\n(DirectQuery — live)"]
    end
    Source1["🗄️ SQL Server\n(live source)"]
    Source2["📁 Excel / CSV\n(static file)"]

    Source2 --> Import1
    Source2 --> Import2
    Source1 <-->|"query on demand"| DQ

    Import1 --> DQ
    Import2 --> DQ
    Report["📊 Power BI Report"]
    DQ --> Report
    Import1 --> Report

    style Import1 fill:#dbeafe,stroke:#3b82f6,color:#1f2937
    style Import2 fill:#dbeafe,stroke:#3b82f6,color:#1f2937
    style DQ fill:#fef3c7,stroke:#f59e0b,color:#1f2937
    style Report fill:#dcfce7,stroke:#22c55e,color:#1f2937
    style Source1 fill:#dbeafe,stroke:#3b82f6,color:#1f2937
    style Source2 fill:#dbeafe,stroke:#3b82f6,color:#1f2937
```

Import tables power fast dimension filtering. DirectQuery delivers live fact data.

## 🔧 How it actually works

A **composite model** lets you combine tables in **Import mode** (data copied into Power BI's in-memory engine) with tables in **DirectQuery mode** (data queried live from the source) — all in the same semantic model. Before composite models existed, you had to choose one mode for the entire report. Now you can pick the right mode per table.

The hybrid car analogy fits: a hybrid uses its battery for short city trips (efficient, instant, quiet) and its engine for long highway runs (necessary when you need more range). Composite models work the same way — put your dimension tables in Import mode for instant filter performance, and put your large or frequently updated fact tables in DirectQuery mode so reports always show current data without waiting for a scheduled refresh.

The practical caveat: when a query involves both Import and DirectQuery tables, Power BI has to query the DirectQuery source and then join the results with the in-memory data — which takes longer than a pure Import query. For most cases this is acceptable. The real power of composite models is pairing a DirectQuery source with pre-built [aggregation tables](aggregation-tables.md) in Import mode, so most queries hit the fast aggregate and only rare detail-level queries fall through to the live source.

## 🌍 Real-world example

A real-time operations dashboard pulls transaction data from a live Azure SQL database (DirectQuery) but filters by product hierarchy and store region from a static dimension file (Import). Dimension slicers are instantaneous; the transaction charts reflect data from the last few seconds. Before composite models, you'd have to choose: accept stale Import data, or accept slow pure-DirectQuery dimension filters.

## 🔗 Related

- [Import vs DirectQuery](import-vs-directquery.md)
- [Aggregation Tables](aggregation-tables.md)
- [Star Schema](star-schema.md)
