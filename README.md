[README-bi-dashboard-templates.md](https://github.com/user-attachments/files/33070726/README-bi-dashboard-templates.md)
# bi-dashboard-templates
bi-dashboard-templates
# bi-dashboard-templates

DAX measure patterns and Power BI star-schema modeling notes from production BI work. Reusable measures you can paste into any model — adjust table and column names to fit.

## DAX patterns

### Year-to-date revenue

```dax
Revenue YTD =
TOTALYTD ( SUM ( FactSales[Revenue] ), DimDate[Date] )
```

### Prior-year same period

```dax
Revenue PY =
CALCULATE (
    SUM ( FactSales[Revenue] ),
    SAMEPERIODLASTYEAR ( DimDate[Date] )
)
```

### Year-over-year % change

```dax
Revenue YoY % =
DIVIDE (
    SUM ( FactSales[Revenue] ) - [Revenue PY],
    [Revenue PY]
)
```

### Month-over-month % change

```dax
Revenue MoM % =
VAR CurrentMonth = SUM ( FactSales[Revenue] )
VAR PriorMonth =
    CALCULATE (
        SUM ( FactSales[Revenue] ),
        PREVIOUSMONTH ( DimDate[Date] )
    )
RETURN
    DIVIDE ( CurrentMonth - PriorMonth, PriorMonth )
```

### Ranking within a group

```dax
Rep Rank =
RANKX (
    ALL ( DimSalesRep[Region] ),
    SUM ( FactSales[Revenue] ),
    ,
    DESC
)
```

### Target attainment

```dax
Attainment % =
DIVIDE (
    SUM ( FactSales[Revenue] ),
    SUM ( DimTargets[TargetAmount] )
)
```

## Modeling notes

These are the conventions behind the measures above:

- Star schema with single-direction relationships (no bidirectional unless required)
- A dedicated date table, marked as the official date table in Power BI
- Measures live in a dedicated `_Measures` table — never in fact tables
- Columns not used in visuals are hidden from report view
- Row-Level Security for governed access; DirectQuery where near-real-time data is needed

## License

MIT © Jeff Rotar
