# financial_forcasting
financial Data Analysis &amp; Outlier Cleaning Analyzing sales performance, discounts, and profit trends while tackling real-world data challenges.The key focus was cleaning messy real-world data (currency formats, missing values) and strategically handling outliers to ensure accurate analysis
# Financial Data Analysis & Outlier Handling
An end-to-end analysis of financial sales data, focusing on data cleaning, outlier detection, and actionable insights.

## Project Overview
This project analyzes a financial dataset containing:
- Sales metrics (Units Sold, Gross Sales, COGS)
- Pricing (Manufacturing Price, Sale Price)
- Discounts and Profit margins
- Segment/Country-wise performance

**Key Focus**: Tackling real-world data challenges like:
- Currency format inconsistencies (`$1,618.50` → `1618.5`)
- Missing/placeholder values (`"N/A"`, `" - "`)
- Strategic outlier handling without losing valid business cases

## Key Features
- **Data Cleaning Pipeline**: Automated formatting fixes for messy financial data
- **Smart Outlier Detection**: IQR-based filtering with dynamic bounds
- **Visual Analytics**: Box plots, trend lines, and segment comparisons
- **Actionable Insights**: Discount impact analysis, high-value transaction identification

## Technical Implementation
### Data Cleaning
```python
# Convert currency strings to numeric values
money_cols = ['Units Sold', 'Gross Sales', 'Profit']
for col in money_cols:
    df[col] = df[col].astype(str).str.replace(r'[\\$,]', '', regex=True)
    df[col] = pd.to_numeric(df[col], errors='coerce')
## Removing Outliers
  --kept few outliers because those are not effecting the data.
