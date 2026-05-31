# Composite Models

## ELI5

Normally you pick one storage mode for your whole Power BI model: either everything is imported into memory (fast) or everything is queried live from the source (always fresh). Composite models let you **mix both** in the same report.

Think of it like a hybrid car. For city driving (your fast-changing operational data), the electric motor (DirectQuery) handles it — always pulling live data. For highway driving (your large historical dimension tables), the petrol engine (Import) takes over — loaded into memory for speed.

You get the freshness of DirectQuery where you need it and the speed of Import everywhere else.

## Visual

```mermaid
flowchart TD
    subgraph Import mode  fast, in-memory
        DimDate[DimDate\nImport]
        DimProduct[DimProduct\nImport]
        DimCustomer[DimCustomer\nImport]
        AggSales[AggSales\nImport aggregation table]
    end

    subgraph DirectQuery mode  always live
        FactSales[FactSales\nDirectQuery]
    end

    DimDate     -->|relationship| FactSales
    DimProduct  -->|relationship| FactSales
    DimCustomer -->|relationship| FactSales
    AggSales    -->|aggregation covers| FactSales
```

## How it works in practice

A finance team needs a report on a 500-million-row transaction table in Azure Synapse. Importing all rows is impractical. But the dimension tables (product, customer, date) are small and change infrequently.

The composite model solution:
- `FactTransactions` — DirectQuery against Synapse (always live, never imported)
- `DimDate`, `DimProduct`, `DimCustomer` — Import mode (loaded into VertiPaq, fast filter performance)
- `AggMonthlySales` — Import aggregation table covering common rolled-up queries

When a visual asks for revenue by product category for the current month, Power BI checks if the aggregation table satisfies the query. If yes, the query hits the in-memory aggregation (milliseconds). If not, it falls through to DirectQuery against Synapse.

### Key facts

- [ ] Composite models require **Power BI Premium** or **Premium Per User** for publishing to the service
- [ ] Import tables in a composite model refresh on a schedule — they are **not** real-time
- [ ] DirectQuery tables generate live SQL queries on every visual render — ensure your source can handle the load
- [ ] Relationships between an Import table and a DirectQuery table are called **limited relationships** — some DAX functions behave differently across them
- [ ] Aggregation tables (Import) can dramatically reduce DirectQuery hits when configured correctly
- [ ] You cannot mix DirectQuery sources from two different databases that are not related unless using a supported gateway configuration
- [ ] Test query performance thoroughly — composite models can produce unexpectedly slow visuals if aggregations are not tuned
