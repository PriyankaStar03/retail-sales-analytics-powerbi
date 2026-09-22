# 🛍️ Retail Sales Analytics \| Power BI

An end-to-end **Retail Sales Analytics** project built with **Power BI,
DAX, data modeling, and interactive data visualization**. The project
transforms transaction-level retail data into an analytical reporting
solution for understanding revenue, profitability, sales volume,
customer activity, product and brand performance, and store performance.

## 📊 Project Overview

This project converts raw retail transactions into an interactive Power
BI reporting solution that enables users to:

-   Monitor revenue, profit, and sales quantity
-   Analyze product, category, sub-category, and brand performance
-   Compare store performance
-   Understand customer activity and ratings
-   Analyze performance using **MTD, QTD, and YTD**
-   Identify top and lower-performing brands
-   Rank stores and other business entities
-   Drill from high-level KPIs into detailed business dimensions

## 🎯 Business Problem

Retail transaction data contains detailed information about sales,
products, customers, stores, prices, quantities, and ratings. However,
raw transactional data makes it difficult to quickly understand:

-   How much revenue is being generated?
-   How profitable are sales?
-   Which products and brands are driving performance?
-   Which stores contribute the most revenue?
-   How is performance changing over time?
-   What is the average customer rating?
-   How much revenue is generated per customer?
-   Which areas require deeper investigation?

The Power BI solution addresses these questions through a structured
analytical model, reusable DAX measures, dynamic time intelligence, and
interactive dashboards.

## 🚀 Project Objectives

1.  Build a structured analytical model from retail transaction data.
2.  Create reusable DAX measures for key business KPIs.
3.  Enable dynamic **MTD / QTD / YTD** analysis.
4.  Compare products, brands, categories, and stores.
5.  Provide Top/Bottom and ranking analysis.
6.  Analyze customer ratings and customer-level revenue.
7.  Create an interactive dashboard for business users.
8.  Provide a foundation for future analytics such as forecasting,
    customer segmentation, and inventory analysis.

## 🗂️ Dataset

  Table               Approx. Rows Purpose
  ----------------- -------------- ----------------------------------------------------
  Customer                   \~40K Customer attributes and segmentation
  Calendar / Date           \~1.5K Time intelligence and date analysis
  Sales_Fact                \~820K Transaction-level sales data
  Store                         55 Store attributes and performance
  Product                      350 Product, category, brand and sub-category analysis

### Key Sales Fields

-   Sales Quantity
-   Cost Price
-   Selling Price
-   Total CP
-   Total SP
-   Profit/Loss
-   Total Profit/Loss
-   Customer Rating
-   Date
-   Product ID
-   Customer ID
-   Store ID

## 🏗️ Data Model

The project follows a **fact-and-dimension / star-style modeling
approach**.

``` text
                    ┌──────────────┐
                    │   Customer   │
                    └──────┬───────┘
                           │
┌────────────┐      ┌──────▼───────┐      ┌────────────┐
│   Product  │─────►│  Sales_Fact  │◄─────│    Store   │
└────────────┘      └──────▲───────┘      └────────────┘
                           │
                    ┌──────┴───────┐
                    │     Date     │
                    └──────────────┘
```

### Core Model Components

-   **Sales_Fact:** Central transaction table
-   **Customer:** Customer-level analysis
-   **Product:** Product, brand, category and sub-category analysis
-   **Store:** Store-level performance
-   **Date:** Time intelligence
-   **Helper / Parameter Tables:** Dynamic reporting controls

## 📈 Key KPIs

  KPI                           Purpose
  ----------------------------- --------------------------------------------
  **Total Revenue**             Measures total sales value
  **Total Profit**              Measures absolute profitability
  **Profit Percentage**         Measures profitability relative to revenue
  **Total Quantity**            Measures sales volume
  **AOV**                       Measures average sales value per fact row
  **Avg Sales/Person**          Measures revenue per distinct customer
  **Average Customer Rating**   Monitors customer experience
  **Top Selling Brand**         Identifies the leading brand
  **Least Selling Brand**       Identifies the lower-performing brand
  **Revenue Rank**              Supports store/entity ranking

## ⏱️ Dynamic Time Intelligence

A disconnected **TimePeriod** parameter allows users to switch between:

-   **MTD:** Month-to-Date
-   **QTD:** Quarter-to-Date
-   **YTD:** Year-to-Date

Example:

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

## 🧮 DAX & Analytics

The project uses a layered DAX approach.

### Base Measures

-   Total Revenue
-   Total Quantity
-   Total Profit
-   Profit Percentage
-   Average Customer Rating
-   Avg Sales/Person
-   AOV

### Advanced Analysis

-   MTD / QTD / YTD
-   Previous-year comparison
-   Top/Bottom analysis
-   Brand analysis
-   Revenue ranking
-   Dynamic parameter-driven calculations

Detailed DAX documentation is available in:

`DAX/measures.md`

## 📑 Dashboard Pages

### 🏠 Home

Landing and navigation page for the report.

### 📊 Overview

Executive-level monitoring of:

-   Revenue
-   Profitability
-   Quantity
-   AOV
-   Customer activity
-   Brand performance
-   Category performance
-   Store revenue
-   Customer ratings

### 🏬 Store Analysis

Detailed analysis using:

-   Decomposition Tree
-   Rating Treemap
-   Quantity Treemap
-   Store analysis
-   Product/category breakdown
-   Store area vs. revenue/profit analysis
-   Interactive slicers

## 🎛️ Interactive Filters

The report provides business-friendly filtering through:

-   Year
-   Month
-   Day
-   MTD / QTD / YTD
-   Category
-   Sub-category
-   Product
-   Brand
-   Store
-   Top / Bottom
-   Display limit

## 🔍 Business Questions Answered

### Sales Performance

-   What is the current sales performance?
-   How much revenue and quantity are being generated?

### Profitability

-   Is sales performance translating into profit?
-   What is the current profit percentage?

### Product & Brand

-   Which brands are performing strongly?
-   Which brands require further investigation?
-   Which categories and products contribute most to sales?

### Store Performance

-   Which stores generate the most revenue?
-   How does store performance relate to store area?

### Customer Analysis

-   What is the average customer rating?
-   How much revenue is generated per customer?

### Time Analysis

-   How does MTD performance compare with QTD and YTD?
-   How does current performance compare with the previous year?

## 🛠️ Technology Stack

-   **Power BI Desktop**
-   **DAX**
-   **Power Query**
-   **Data Modeling**
-   **Excel**
-   **Data Visualization**
-   **Time Intelligence**

## 📁 Repository Structure

``` text
retail-sales-analytics-powerbi/
│
├── README.md
│
├── PowerBI/
│   └── Retail_Sales.pbix
│
├── Data/
│   └── Retail_data.xlsx
│   └── Retail_fact.xlsx
│
├── DAX/
│   └── measures.md
│
├── Documentation/
│   └── Retail_Sales_PowerBI_Project_Documentation.docx
│
└── Screenshots/
    └── dashboard screenshots
```

## 🧪 Data Quality & Validation

The project includes validation considerations for:

-   Fact-to-dimension key matching
-   Date relationships
-   Missing Product, Customer, Store and Date values
-   Revenue consistency
-   Profit calculations
-   Quantity calculations
-   Rating ranges
-   MTD/QTD/YTD behavior
-   Slicer interactions
-   Ranking and Top/Bottom behavior
-   Visual formatting and performance

## 💡 Analytical Approach

A KPI is treated as a **signal rather than a diagnosis**.

  -----------------------------------------------------------------------
  Signal                              Follow-up Analysis
  ----------------------------------- -----------------------------------
  High revenue but low profit %       Review price, cost, product and
                                      store mix

  High quantity but low revenue       Review lower-priced products and
                                      category mix

  Low brand performance               Review assortment, store presence,
                                      ratings and trends

  Low store revenue                   Review store area, product mix and
                                      customer activity

  Low customer rating                 Drill into product, category and
                                      store
  -----------------------------------------------------------------------

This encourages users to move from **"What happened?"** to **"Where
should we investigate further?"**

## 📚 Project Documentation

Detailed project documentation covers:

-   Business requirements
-   Data understanding
-   Data model
-   DAX measures
-   KPI framework
-   Dashboard design
-   Business questions
-   Data quality checks
-   Testing
-   Deployment considerations
-   End-to-end project flow

See:

`Documentation/Retail_Sales_PowerBI_Project_Documentation.docx`

## 🔮 Future Enhancements

The solution can be extended with:

-   Year-over-Year growth analysis
-   Sales targets and budgets
-   Customer segmentation
-   Customer lifetime value
-   Cohort analysis
-   Inventory analytics
-   Store productivity metrics
-   Sales forecasting
-   Anomaly detection
-   Automated business alerts

## 👩‍💻 Author

**Priyanka**

Data Analyst \| Power BI \| SQL \| Python \| Data Analytics

------------------------------------------------------------------------

### ⭐ Project Highlights

**\~820K sales transactions \| \~40K customers \| 350 products \| 55
stores**

Built as an end-to-end portfolio project demonstrating **business
analysis, dimensional modeling, DAX, time intelligence, KPI design, and
interactive Power BI reporting**.
