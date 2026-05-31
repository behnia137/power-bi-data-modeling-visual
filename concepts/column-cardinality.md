# Column Cardinality

## ELI5

**Column cardinality** is simply: how many unique values does this column have?

A `Gender` column with values "Male," "Female," and "Non-binary" has a cardinality of 3 — very low. A `TransactionID` column with one unique value per row in a 50-million-row table has a cardinality of 50 million — very high.

This matters enormously in Power BI because VertiPaq (the in-memory engine) compresses low-cardinality columns incredibly well and barely compresses high-cardinality columns at all. A single high-cardinality column can consume more RAM than an entire well-designed model.

## Visual

```mermaid
flowchart TD
    subgraph Low cardinality — compresses extremely well
        C1["Country\n~200 unique values\nVertiPaq stores: 200 values + row index"]
        C2["Status\n3 unique values\nVertiPaq stores: 3 values + row index"]
    end

    subgraph High cardinality — compresses poorly
        C3["TransactionID\n50M unique values\nVertiPaq stores: 50M values — no compression"]
        C4["Timestamp with seconds\n86,400 unique values per day\nVertiPaq stores: very large dictionary"]
    end
```

## How it works in practice

A developer imports an order table with these columns:

| Column | Unique values | Impact |
|---|---|---|
| `OrderID` | 5,000,000 | High — large dictionary, poor compression |
| `CustomerID` | 250,000 | Medium |
| `ProductSKU` | 12,000 | Medium-low |
| `OrderStatus` | 4 | Very low — compresses nearly to zero |
| `OrderTimestamp` | 4,320,000 | Very high — seconds precision blows up the dictionary |
| `OrderDate` | 1,826 | Low — date-only is fine |

**Optimization actions:**
- Remove `OrderID` from the fact table if it is never used in visuals or relationships — it wastes significant memory
- Truncate `OrderTimestamp` to minute or hour precision if second-level granularity is not needed
- Replace free-text columns like `Notes` with a category code when possible

**Checking cardinality in DAX:**
```dax
-- Measure to audit cardinality of a column
Distinct OrderIDs = DISTINCTCOUNT(FactOrders[OrderID])
```

Or use **DAX Studio** → **VertiPaq Analyzer** to see column-level size and cardinality for the entire model.

### Key facts

- [ ] High-cardinality columns are the **number one cause of bloated Power BI model sizes**
- [ ] A column that is unique per row (like a GUID or timestamp with seconds) provides almost zero compression benefit in VertiPaq
- [ ] Remove columns that are **never used** in visuals, relationships, or measures — they consume memory for no benefit
- [ ] Date columns stored as `DateTime` have far higher cardinality than `Date` — strip the time component unless needed
- [ ] Free-text columns (comments, descriptions) have very high cardinality and large dictionary sizes — consider excluding them
- [ ] **DAX Studio's VertiPaq Analyzer** is the best tool to identify high-cardinality columns in a live model
- [ ] Reducing column cardinality is often more impactful on model size than reducing row count
