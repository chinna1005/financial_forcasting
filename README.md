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
code:
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
df=pd.read_csv("C:\\Users\\sares\\Downloads\\Financials.csv")

# Clean column names
df.columns = df.columns.str.strip()

# List of money-related columns to clean
money_columns = ['Units Sold', 'Manufacturing Price', 'Sale Price',
                 'Gross Sales', 'Discounts', 'Sales', 'COGS', 'Profit']
for col in money_columns:
    df[col] = df[col].astype(str).str.replace(r'[\$,]', '', regex=True)     # Remove $ and commas
    df[col] = df[col].replace(['-', ' - ', ' -', '- ', ' -   '], pd.NA)      # Replace dash-like entries with NaN
    df[col] = pd.to_numeric(df[col], errors='coerce')                       # Convert to float

    df['Date'] = pd.to_datetime(df['Date'])

# Sort by date
df = df.sort_values(by='Date')
print(df)
Q1 = df['Profit'].quantile(0.25)
Q3 = df['Profit'].quantile(0.75)

print("Q1:", Q1)
print("Q3:", Q3)

low_bound=Q1-1.5*IQR
upper_bound=Q3+1.5*IQR
df_outlier=df[(df['Profit']<low_bound) | (df['Profit'] > upper_bound)]
print(df_outlier)
## box plot without outliers
Q1=df['Profit'].quantile(0.25)
Q3=df['Profit'].quantile(0.75)
IQR=Q3-Q1
low_bound=Q1 - 1.5 * IQR
upper_bound=Q3 + 1.5 *IQR
df_outlier = df[(df['Profit'] < low_bound) | (df['Profit'] > upper_bound)]

sns.boxplot(data=df_outlier, x='Product', y='Profit')
plt.title('Box Plot Without Outlier')
plt.xlabel('Product')
plt.ylabel('Profit')
plt.xticks(rotation=45)
plt.show()

# 1. Verify column names first
print("DataFrame columns:", df_final_clean.columns.tolist())

# 2. Ensure correct column names (adjust if needed)
# If your column is named differently, change these:
x_col = 'Product'  # Change if your category column has a different name
y_col = 'Profit'   # Change if your value column has a different name

# 3. Final visualization with error handling
plt.figure(figsize=(14, 7))

try:
    # Create boxplot without hue to avoid duplication
    ax = sns.boxplot(
        data=df_final_clean,
        x=x_col,
        y=y_col,
        palette='viridis',
        showfliers=False
    )
    
    # Add data points
    sns.stripplot(
        data=df_final_clean,
        x=x_col,
        y=y_col,
        color='black',
        size=4,
        alpha=0.5,
        jitter=0.2
    )
    
    # Add median labels
    medians = df_final_clean.groupby(x_col)[y_col].median()
    for i, (product, median) in enumerate(medians.items()):
        ax.text(i, median, f'Median: {median:,.0f}', 
                ha='center', va='center', fontsize=9,
                bbox=dict(facecolor='white', alpha=0.8))
    
    # Formatting
    plt.title('Final Cleaned Profit Distribution\n(No Negatives & Extreme Outliers)', pad=20)
    plt.xlabel('Product Category', labelpad=12)
    plt.ylabel('Profit ($)', labelpad=12)
    plt.xticks(rotation=45)
    plt.grid(axis='y', alpha=0.3)
    plt.ylim(0, df_final_clean[y_col].quantile(0.99)*1.1)  # Auto-scale
    
    plt.tight_layout()
    plt.show()

except Exception as e:
    print(f"Visualization error: {str(e)}")
    print("Please verify your DataFrame contains columns:", [x_col, y_col])
    print("Actual columns:", df_final_clean.columns.tolist())

# If you prefer not to set Date as index:
monthly_profit = df.groupby(pd.Grouper(key='Date', freq='MS'))['Profit'].sum()

print("Monthly Profit (alternative method):")
print(monthly_profit)
## monthly profits
monthly_profit.plot(kind='bar', figsize=(10, 6))
plt.title('Monthly Profit Trends')
plt.ylabel('Total Profit')
plt.xlabel('Month')
plt.xticks(rotation=45)
plt.grid(True, axis='y')
plt.show()
## Rolling statistics to identify trends
plt.figure(figsize=(14,7))
ts.plot(label='Actual', color='#1f77b4')
ts.rolling(window=6).mean().plot(label='6-Month Rolling Mean', color='#ff7f0e')
ts.rolling(window=6).std().plot(label='6-Month Rolling Std', color='#2ca02c')
plt.title('Profit Trend Analysis', pad=20, fontsize=14)
plt.legend()
plt.grid(True, linestyle='--', alpha=0.7)
plt.show()
## seasonal forecasting
# 1. Prepare data
ts = ts_sales.copy()

# 2. Calculate 3-month moving average
forecast_steps = 12
ma_window = 3  # Use last 3 months to forecast

# 3. Create forecasts
forecasts = []
for i in range(forecast_steps):
    # Last available moving average
    last_ma = ts.rolling(window=ma_window).mean().iloc[-1]
    forecasts.append(last_ma)
    
    # Append forecast to series (for next prediction)
    next_date = ts.index[-1] + pd.DateOffset(months=1)
    ts = pd.concat([ts, pd.Series([last_ma], index=[next_date])])

# 4. Get forecast dates
forecast_index = pd.date_range(ts_sales.index[-1] + pd.DateOffset(months=1), 
                              periods=forecast_steps, 
                              freq='MS')

# 5. Plot results
plt.figure(figsize=(12,6))
plt.plot(ts_sales.index, ts_sales, 'bo-', label='Historical Sales')
plt.plot(forecast_index, forecasts, 'ro--', label='3-Month MA Forecast')
plt.title('Sales Forecast (3-Month Moving Average)', pad=15)
plt.ylabel('Sales ($)')
plt.xlabel('Date')
plt.legend()
plt.grid(alpha=0.3)
plt.tight_layout()
plt.show()

# 6. Create forecast table
forecast_df = pd.DataFrame({
    'Date': forecast_index.strftime('%b %Y'),
    'Forecast': [round(f, 2) for f in forecasts]
}).set_index('Date')

print("Forecast Results:")
print(forecast_df)

# Confirm fix
df[money_columns].dtypes
# Remove unwanted characters
df['Profit'] = df['Profit'].astype(str).str.replace(r'[\$,]', '', regex=True)
df['Profit'] = df['Profit'].replace(['-', ' - ', 'N/A', '', 'nan'], pd.NA)
df['rofit'] = pd.to_numeric(df['profit'], errors='coerce')

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
