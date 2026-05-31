# Cross-Filter Direction

## ELI5

Imagine water flowing through a pipe. In a normal star schema, filters flow **downhill** — from the dimension table into the fact table. A slicer on `DimProduct[Category]` pushes a filter into `FactSales` and you see the right revenue.

Now imagine the pipe has a valve that lets water flow both ways. That is **bidirectional** cross-filtering. A filter on `FactSales` can now travel back up into `DimProduct` — which means a slicer showing product categories will only show categories that have at least one sale.

That sounds useful, but if you have multiple fact tables connected to the same dimension, the water starts flowing in unexpected directions and your numbers break. Use bidirectional filtering with caution.

## Visual

```mermaid
flowchart LR
    subgraph Single direction  safe default
        DC1[DimCustomer] -->|filter flows this way| FS1[FactSales]
    end

    subgraph Bidirectional  use carefully
        DC2[DimCustomer] <-->|filters flow both ways| FS2[FactSales]
    end

    subgraph Ambiguity problem
        DC3[DimDate] <-->|bidirectional| FS3[FactSales]
        DC3 <-->|bidirectional| FS4[FactReturns]
        FS3 -.->|unexpected cross-contamination| FS4
    end
```

## How it works in practice

**Scenario — Counting distinct active customers:**

A common need is to show only customers who have made at least one purchase. With single-direction filtering, a visual on `DimCustomer` cannot be filtered by `FactSales`. Options:

1. Use `CROSSFILTER()` in a DAX measure to temporarily enable bidirectional filtering
2. Add a calculated column to `DimCustomer` that flags active customers using `RELATED`
3. Enable bidirectional on that specific relationship (acceptable when there is only one fact table in the chain)

**Scenario — Multiple facts sharing a dimension:**

If `DimDate` is shared between `FactSales` and `FactBudget`, enabling bidirectional on both relationships creates a path where a filter on `FactSales` can leak into `FactBudget`. This produces nonsensical budget figures when any sales slicer is active.

### Key facts

- [ ] Default cross-filter direction is **Single** — filters flow from the "one" side to the "many" side only
- [ ] **Bidirectional** allows filters to travel in both directions across the relationship
- [ ] Bidirectional is safe when a dimension connects to **exactly one fact table**
- [ ] Bidirectional on a shared dimension (used by two or more fact tables) almost always causes **filter ambiguity**
- [ ] Power BI will warn about ambiguous filter paths — do not dismiss this warning
- [ ] Prefer `CROSSFILTER()` in DAX over always-on bidirectional to keep the default model behavior predictable
- [ ] The "Apply security filter in both directions" option on RLS is a separate setting — do not confuse it with cross-filter direction
