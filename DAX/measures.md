[measures.md](https://github.com/user-attachments/files/32507371/measures.md)
# Retail Sales Analytics: DAX Measures

This document contains the core DAX measures and calculation patterns
documented for the **Retail Sales Analytics Power BI project**.

> **Note:** The formulas below are based on the project's documented DAX
> logic. Where the project documentation identifies a measure but does
> not provide its exact internal expression, it is listed as a
> documented measure/pattern rather than an invented formula.

------------------------------------------------------------------------

## 1. Base Measures

### Total Revenue

Calculates total sales revenue from the transaction fact table.

``` dax
Total Revenue =
SUM ( Sales_Fact[Total SP] )
```

### Total Quantity

Calculates total units sold.

``` dax
Total Quantity =
SUM ( Sales_Fact[Sales Quantity] )
```

### Total Profit

Calculates total profit/loss.

``` dax
Total Profit =
SUM ( Sales_Fact[Total prof/loss] )
```

------------------------------------------------------------------------

## 2. Profitability Measures

### Profit Percentage

Calculates profit as a percentage of revenue.

``` dax
Profit Percentage =
DIVIDE (
    [Total Profit],
    [Total Revenue],
    0
)
```

**Business purpose:** Measures profitability rather than sales volume
alone.

------------------------------------------------------------------------

## 3. Customer & Transaction Measures

### Average Customer Rating

Calculates the average customer rating within the current filter
context.

``` dax
Average Customer Rating =
AVERAGE ( Sales_Fact[Customer Rating] )
```

### Avg Sales/Person

Calculates revenue generated per distinct customer.

``` dax
Avg Sales/Person =
DIVIDE (
    [Total Revenue],
    DISTINCTCOUNT ( Sales_Fact[Customer ID] ),
    0
)
```

### AOV

Calculates average sales value per fact row.

``` dax
AOV =
DIVIDE (
    [Total Revenue],
    COUNTROWS ( Sales_Fact ),
    0
)
```

> **Important:** The documented model does not contain a separate Order
> ID. Therefore, this AOV represents an average fact-row sales value
> unless the organization defines another order-grain rule.

------------------------------------------------------------------------

# 4. Dynamic MTD / QTD / YTD Measures

The report uses a disconnected `mtd/qtd/ytd` parameter table with a
`TimePeriod` field. This allows a single visual to switch between
Month-to-Date, Quarter-to-Date, and Year-to-Date calculations.

## Total Revenue MTD/QTD/YTD

``` dax
Total Revenue MTD/QTD/YTD =
SWITCH (
    SELECTEDVALUE ( 'mtd/qtd/ytd'[TimePeriod], "MTD" ),
    "MTD", CALCULATE (
        [Total Revenue],
        DATESMTD ( 'Date'[Date] )
    ),
    "QTD", CALCULATE (
        [Total Revenue],
        DATESQTD ( 'Date'[Date] )
    ),
    "YTD", CALCULATE (
        [Total Revenue],
        DATESYTD ( 'Date'[Date] )
    ),
    [Total Revenue]
)
```

## Sum Sales Quantity MTD/QTD/YTD

``` dax
Sum Sales Quantity MTD/QTD/YTD =
SWITCH (
    SELECTEDVALUE ( 'mtd/qtd/ytd'[TimePeriod], "MTD" ),
    "MTD", CALCULATE (
        [Total Quantity],
        DATESMTD ( 'Date'[Date] )
    ),
    "QTD", CALCULATE (
        [Total Quantity],
        DATESQTD ( 'Date'[Date] )
    ),
    "YTD", CALCULATE (
        [Total Quantity],
        DATESYTD ( 'Date'[Date] )
    ),
    [Total Quantity]
)
```

## Total Profit MTD/QTD/YTD

``` dax
Total Profit MTD/QTD/YTD =
SWITCH (
    SELECTEDVALUE ( 'mtd/qtd/ytd'[TimePeriod], "MTD" ),
    "MTD", CALCULATE (
        [Total Profit],
        DATESMTD ( 'Date'[Date] )
    ),
    "QTD", CALCULATE (
        [Total Profit],
        DATESQTD ( 'Date'[Date] )
    ),
    "YTD", CALCULATE (
        [Total Profit],
        DATESYTD ( 'Date'[Date] )
    ),
    [Total Profit]
)
```

## Avg_Rating MTD/QTD/YTD

``` dax
Avg_Rating MTD/QTD/YTD =
SWITCH (
    SELECTEDVALUE ( 'mtd/qtd/ytd'[TimePeriod], "MTD" ),
    "MTD", CALCULATE (
        [Average Customer Rating],
        DATESMTD ( 'Date'[Date] )
    ),
    "QTD", CALCULATE (
        [Average Customer Rating],
        DATESQTD ( 'Date'[Date] )
    ),
    "YTD", CALCULATE (
        [Average Customer Rating],
        DATESYTD ( 'Date'[Date] )
    ),
    [Average Customer Rating]
)
```

------------------------------------------------------------------------

# 5. Brand Analysis

## Top Selling Brand

The documented pattern identifies the brand with the highest revenue in
the current filter context.

``` dax
Top Selling Brand =
VAR BrandTable =
    ADDCOLUMNS (
        VALUES ( Product[Brand] ),
        "@Revenue", [Total Revenue]
    )
VAR TopBrand =
    TOPN (
        1,
        BrandTable,
        [@Revenue],
        DESC
    )
RETURN
    CONCATENATEX (
        TopBrand,
        Product[Brand],
        ", "
    )
```

**Metric used:** Revenue.

> The business can instead define "selling" using quantity by replacing
> `[Total Revenue]` with `[Total Quantity]`.

## Least Selling Brand

The documented pattern identifies the brand with the lowest revenue in
the current filter context.

``` dax
Least Selling Brand =
VAR BrandTable =
    ADDCOLUMNS (
        VALUES ( Product[Brand] ),
        "@Revenue", [Total Revenue]
    )
VAR BottomBrand =
    TOPN (
        1,
        BrandTable,
        [@Revenue],
        ASC
    )
RETURN
    CONCATENATEX (
        BottomBrand,
        Product[Brand],
        ", "
    )
```

------------------------------------------------------------------------

# 6. Previous-Period / Previous-Year Analysis

## Previous Year Revenue

Calculates revenue for the corresponding period in the previous year.

``` dax
Previous Year Revenue =
CALCULATE (
    [Total Revenue],
    SAMEPERIODLASTYEAR ( 'Date'[Date] )
)
```

The PBIX documentation also identifies a dedicated **`_prev measure`**
area for previous-period and previous-year comparison logic.

------------------------------------------------------------------------

# 7. Ranking & Top-N Analysis

## Revenue Rank

Ranks stores by revenue while respecting the report's external slicer
selections.

``` dax
Revenue Rank =
RANKX (
    ALLSELECTED ( Store[Store Name] ),
    [Total Revenue],
    ,
    DESC,
    DENSE
)
```

### How it works

-   `RANKX` calculates the ranking.
-   `ALLSELECTED` preserves relevant external slicer selections.
-   `[Total Revenue]` is the ranking metric.
-   `DESC` ranks the highest revenue as rank 1.
-   `DENSE` avoids gaps in ranking when values are tied.

The same pattern can be adapted for:

-   Stores
-   Brands
-   Products
-   Categories
-   Sub-categories

------------------------------------------------------------------------

# 8. Documented KPI Measure Inventory

The PBIX documentation identifies the following measures and measure
groups.

  -----------------------------------------------------------------------
  Measure                             Purpose
  ----------------------------------- -----------------------------------
  Total Revenue                       Base revenue KPI

  Total Revenue MTD/QTD/YTD           Dynamic revenue KPI

  Total Quantity                      Base quantity KPI

  Sum Sales Quantity MTD/QTD/YTD      Dynamic quantity KPI

  Total Profit                        Base profit KPI

  Total Profit MTD/QTD/YTD            Dynamic profit KPI

  Profit Percentage                   Profitability KPI

  AOV                                 Average transaction/fact-row value

  AOV MTD/QTD/YTD                     Dynamic AOV KPI

  Avg Sales/Person                    Customer-level revenue productivity

  Avg Sales/Person MTD/QTD/YTD        Dynamic customer productivity KPI

  Average Customer Rating             Customer experience KPI

  Avg_Rating MTD/QTD/YTD              Dynamic rating KPI

  Top Selling Brand                   Highest-performing brand under
                                      selected metric

  Top Selling Brand MTD/QTD/YTD       Dynamic top-brand KPI

  Least Selling Brand                 Lowest-performing brand under
                                      selected metric

  Least Selling Brand MTD/QTD/YTD     Dynamic least-brand KPI

  Top Selling Brand                   Base brand-analysis measure

  prev_Top Selling Brand              Previous comparison brand measure

  prev_Top Selling Brand MTD/QTD/YTD  Dynamic previous comparison brand
                                      measure

  Previous Year Revenue               Previous-year comparison

  Revenue Rank                        Ranking / Top-N analysis
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 9. Parameter & Helper Tables

The report uses several helper objects to make the dashboard
interactive.

  -----------------------------------------------------------------------
  Helper / Table                      Purpose
  ----------------------------------- -----------------------------------
  `mtd/qtd/ytd`                       Disconnected TimePeriod selector
                                      for MTD, QTD and YTD

  `TopBottom`                         Controls Top/Bottom analysis

  `no_parameter`                      Numeric limit/control for displayed
                                      entities

  `_Measure_`                         Dedicated organization area for
                                      reusable business measures

  `_prev measure`                     Organization area for
                                      previous-period / previous-year
                                      measures

  `Breakdown`                         Supports analytical breakdown
                                      behavior
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 10. Time Intelligence

The report uses the following standard Power BI time-intelligence
functions:

### MTD

``` dax
DATESMTD ( 'Date'[Date] )
```

Used to calculate Month-to-Date performance.

### QTD

``` dax
DATESQTD ( 'Date'[Date] )
```

Used to calculate Quarter-to-Date performance.

### YTD

``` dax
DATESYTD ( 'Date'[Date] )
```

Used to calculate Year-to-Date performance.

### Previous Year

``` dax
SAMEPERIODLASTYEAR ( 'Date'[Date] )
```

Used to retrieve the corresponding previous-year period.

------------------------------------------------------------------------

# 11. DAX Design Approach

The project follows a layered DAX approach:

``` text
Source Data
    ↓
Base Measures
    ↓
Time Intelligence / Comparison Logic
    ↓
Ranking & Dynamic Analysis
    ↓
Interactive Power BI Visuals
```

### Base layer

Simple reusable calculations such as:

-   Revenue
-   Quantity
-   Profit
-   Rating
-   Customer-level sales

### Analytical layer

Calculations built on top of the base measures:

-   MTD
-   QTD
-   YTD
-   Previous Year
-   Top / Bottom
-   Ranking

### Presentation layer

The measures are consumed by:

-   KPI cards
-   Charts
-   Treemaps
-   Decomposition tree
-   Store analysis
-   Brand analysis
-   Category analysis
-   Interactive slicers

------------------------------------------------------------------------

# 12. Important DAX & Business Notes

1.  **AOV definition:** The documented model does not expose a separate
    Order ID. Therefore, the current AOV uses `COUNTROWS(Sales_Fact)` as
    its denominator.

2.  **Top/Least Selling Brand:** The documented examples use revenue as
    the default metric. The business definition can instead use
    quantity.

3.  **Filter context:** The measures are designed to respond to filters
    such as Year, Month, Category, Sub-category, Product, Brand, Store,
    and other report selections.

4.  **Date table:** MTD/QTD/YTD calculations depend on a properly
    configured Date table and date relationship.

5.  **Ranking:** `ALLSELECTED` is used where ranking should respect the
    user's external slicer selections.

6.  **Validation:** KPI calculations should be validated against
    independently calculated source values before production sign-off.

------------------------------------------------------------------------

# 13. Technologies Used

-   **Power BI Desktop**
-   **DAX**
-   **Power Query / Data Preparation**
-   **Data Modeling**
-   **Time Intelligence**
-   **Interactive Data Visualization**
-   **Excel**

------------------------------------------------------------------------

## Project Context

This DAX layer is part of the **Retail Sales Analytics** project, which
analyzes transaction-level retail data across:

-   Revenue
-   Profitability
-   Sales quantity
-   Products
-   Categories
-   Sub-categories
-   Brands
-   Stores
-   Customers
-   Customer ratings
-   Time periods

The report uses a fact-and-dimension model consisting of `Sales_Fact`,
`Customer`, `Product`, `Store`, and `Date` tables, with helper tables
supporting dynamic reporting behavior.
