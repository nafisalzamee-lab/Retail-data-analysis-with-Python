# Large-Scale E-commerce Sales Data Analysis with Python Pandas

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-11557c?style=for-the-badge&logo=matplotlib&logoColor=white)

## 📊 Overview

This project demonstrates an end-to-end data analysis workflow on a real-world e-commerce dataset, focusing on **exploratory data analysis (EDA)**, **customer segmentation**, and **performance optimization** for large datasets. Utilizing a dataset of half a million sales transactions from a UK-based online retailer, the project uncovers key business insights and showcases proficiency in handling big data challenges.

A significant aspect of this project involves scaling the analysis utilizing the efficiency of Pandas for large-scale data processing.

## 🌟 Key Features & Methodologies

### 1. Data Acquisition & Loading

* Sourced a dataset of approximately 500,000 sales transactions from a UK online retailer, including details such as `Invoice Number`, `Stock Code`, `Description`, `Quantity`, `Invoice Date`, `Unit Price`, and `Customer ID`.

* Loaded the data into a Pandas DataFrame with explicit type specifications for optimization.

```python
# Downloading and unzipping the dataset
!wget https://archive.ics.uci.edu/ml/machine-learning-databases/00352/Online%20Retail.xlsx
!unzip Online\ Retail.xlsx.zip

# Importing libraries
import pandas as pd
import matplotlib.pyplot as plt

# Loading the Excel file into a DataFrame with explicit dtypes
dtype_dict = {
    'InvoiceNo': str,
    'StockCode': str,
    'Description': str,
    'Quantity': int,
    'CustomerID': str
}
df1 = pd.read_excel('Online Retail.xlsx', dtype=dtype_dict)
print(df1.shape)
print(df1.dtypes)
df1.head()
```

### 2. Robust Data Cleaning & Preprocessing

* **Missing Value Imputation**: Replaced missing `Description` values by finding the most frequent description per `StockCode` and using it for imputation.

```python
# Identify missing descriptions
missing_desc = df1['Description'].isnull().sum()

# Get most frequent description per StockCode
most_frequent = df1.groupby('StockCode')['Description'] \
    .agg(lambda x: x.mode().iloc[0] if not x.mode().empty else None) \
    .reset_index() \
    .rename(columns={'Description': 'FrequentDescription'})

# Merge with original DataFrame
df2 = df1.merge(most_frequent, on='StockCode', how='left')

# Replace Description with FrequentDescription
df2['Description'] = df2['FrequentDescription']

# Drop helper column
df2.drop(columns=['FrequentDescription'], inplace=True)

# Drop rows with any remaining null descriptions
df2.dropna(subset=['Description'], inplace=True)
```

* **Handling Invalid Entries**: Removed records with negative `Quantity` or `Unit Price` values, identified as errors.

```python
# Filter out negative Quantity and Unit Price
df3 = df2[(df2['Quantity'] > 0) & (df2['UnitPrice'] > 0)]
```

* **Outlier Consideration**: Examined extreme `Quantity` values and retained them based on business context (e.g., wholesale orders).

```python
# Check 99th percentile for Quantity
quantile_99 = df3['Quantity'].quantile(0.99)

# Optionally filter out extreme outliers beyond 99th percentile
# df3 = df3[df3['Quantity'] <= quantile_99]
```

### 3. Feature Engineering for Enhanced Analysis

* Created a new `TotalSales` column by multiplying `Quantity` and `UnitPrice`.

```python
df4 = df3.copy()
df4['TotalSales'] = df4['Quantity'] * df4['UnitPrice']
```

* Extracted the `Month` from `InvoiceDate` for time-based trend analysis.

```python
df4['InvoiceDate'] = pd.to_datetime(df4['InvoiceDate'])
df4['Month'] = df4['InvoiceDate'].dt.month
```

### 4. Exploratory Data Analysis (EDA) & Visualization

* **Monthly Sales Trend Analysis**: Grouped sales by month and plotted trends.

```python
monthly_sales = df4.groupby('Month')['TotalSales'].sum()

plt.figure(figsize=(10,6))
monthly_sales.plot(kind='line', marker='o')
plt.title('Monthly Sales Trend')
plt.xlabel('Month')
plt.ylabel('Total Sales')
plt.grid(True)
plt.show()
```

* **Geographical Sales Distribution**: Analyzed top countries by total sales and their percentage contributions.

```python
country_sales = df4.groupby('Country')['TotalSales'].sum().sort_values(ascending=False)
top5_countries = country_sales.head(5)

# Calculate percentage contribution
total_sales_sum = country_sales.sum()
percentage_contrib = (top5_countries / total_sales_sum) * 100

# Plot horizontal bar chart
percentage_contrib.plot(kind='barh', figsize=(10,6), color='skyblue')
plt.title('Top 5 Countries by Sales Percentage')
plt.xlabel('Percentage of Total Sales')
plt.show()
```

* **Product Performance Analysis**: Identified top-selling products by `StockCode` and `Description`.

```python
top_products = df4.groupby(['StockCode', 'Description'])['TotalSales'].sum().sort_values(ascending=False).head(5)
print(top_products)
```

### 5. Advanced Customer Analytics (RFM & Churn Analysis)

* **RFM Analysis**: Calculated Recency, Frequency, and Monetary values per customer.

```python
import datetime as dt

# Define current date as one day after max invoice date
current_date = df4['InvoiceDate'].max() + pd.Timedelta(days=1)

rfm = df4.groupby('CustomerID').agg({
    'InvoiceDate': lambda x: (current_date - x.max()).days,
    'InvoiceNo': 'nunique',
    'TotalSales': 'sum'
}).reset_index()

rfm.columns = ['CustomerID', 'Recency', 'Frequency', 'Monetary']
```

* Created quartiles for RFM scores and calculated an overall RFM score.

```python
rfm['R_Quartile'] = pd.qcut(rfm['Recency'], 4, labels=[4,3,2,1])
rfm['F_Quartile'] = pd.qcut(rfm['Frequency'], 4, labels=[1,2,3,4])
rfm['M_Quartile'] = pd.qcut(rfm['Monetary'], 4, labels=[1,2,3,4])

rfm['RFM_Score'] = rfm[['R_Quartile', 'F_Quartile', 'M_Quartile']].sum(axis=1)
```

* **Customer Churn Analysis**:
