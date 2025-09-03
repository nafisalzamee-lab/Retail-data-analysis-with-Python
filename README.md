# Large-Scale E-commerce Sales Data Analysis with Python Pandas

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Matplotlib](https://img.shields.io/badge/Matplotlib-11557c?style=for-the-badge&logo=matplotlib&logoColor=white)

[![YouTube](https://img.shields.io/badge/Watch%20on-YouTube-red?logo=youtube)](https://youtu.be/X4ee_lMhEmU)

[![Watch the video](Video%20thumbnail.png)](https://youtu.be/X4ee_lMhEmU)

---
## 📊 Overview

This project demonstrates an end-to-end data analysis workflow on a real-world e-commerce dataset, focusing on **exploratory data analysis (EDA)**, **customer segmentation**, and **performance optimization** for large datasets. Utilizing a dataset of half a million sales transactions from a UK-based online retailer, the project uncovers key business insights and showcases proficiency in handling big data challenges.

A significant aspect of this project involves scaling the analysis utilizing the efficiency of Pandas for large-scale data processing.

---

## Data Set Details

The dataset used in the project contains approximately half a million records with the following columns:

*   **Invoice Number**
*   **Stock Code** (Product ID)
*   **Description**
*   **Quantity**
---
### Tools & Technologies

*   **Python**: Primary programming language.
*   **Pandas**: For data manipulation and analysis.
*   **Matplotlib**: For data visualization.
*   **Google Colab**: Cloud-based Jupyter environment with AI assistance.

---
## Key Analyses Performed

The project covers various types of analysis:

*   **Trend Analysis**: Analyzing sales trends over time.
*   **Customer Segmentation**: Using RFM (Recency, Frequency, Monetary) analysis to segment customers.
*   **Customer Churn Analysis**: Identifying customers who are likely to stop buying from the retailer.

---


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

*   **Customer Churn Analysis**: Classified customers as churned if `Recency` exceeded 90 days.

```python
churn_threshold = 90
rfm['Churn'] = rfm['Recency'] > churn_threshold

# Visualize churn distribution
rfm['Churn'].value_counts().plot(kind='bar', color=['green', 'red'])
plt.title('Customer Churn Distribution')
plt.xlabel('Churned')
plt.ylabel('Number of Customers')
plt.show()
```

---

## Key Findings and Insights with Python Code & Visuals

---

### 1. Monthly Sales Trends

**Finding:**  
Sales were initially lower but significantly increased in the last few months of the year (months 10, 11, and 12). This "hockey curve" trend is likely due to the holiday season (Christmas time in the UK) and potential year-end sales incentives or inventory clearance efforts.

**Code and Visualization:**

```python
# Grouping total sales by month
monthly_sales = df4.groupby('Month')['TotalSales'].sum()

# Plotting monthly sales trend as a line chart with markers
plt.figure(figsize=(10,6))
monthly_sales.plot(kind='line', marker='o')
plt.title('Monthly Sales Trend')
plt.xlabel('Month')
plt.ylabel('Total Sales')
plt.grid(True)
plt.show()
```

*Visual: Line chart showing monthly sales increasing sharply towards months 10, 11, and 12*   

---

### 2. Geographical Sales Dependency

**Finding:**  
The UK contributes approximately 84.6% of total sales, indicating high market dependency. This suggests potential business risk and opportunity for diversification.

**Code and Visualization:**

```python
# Grouping total sales by country and sorting descending
country_sales = df4.groupby('Country')['TotalSales'].sum().sort_values(ascending=False)

# Top 5 countries by sales
top5_countries = country_sales.head(5)

# Calculate percentage contribution of top 5 countries
total_sales_sum = country_sales.sum()
percentage_contrib = (top5_countries / total_sales_sum) * 100

# Plot horizontal bar chart for percentage contribution
percentage_contrib.plot(kind='barh', figsize=(10,6), color='skyblue')
plt.title('Top 5 Countries by Sales Percentage')
plt.xlabel('Percentage of Total Sales')
plt.show()
```

*Visual: Horizontal bar chart showing UK dominating sales percentage, followed by Netherlands and others*   

---

### 3. Product Sales Diversification

**Finding:**  
The top-selling product ("Postage") accounts for only about 1.9% to 2% of total product sales, indicating good product diversification with no excessive dependency on a single product.

**Code and Visualization:**

```python
# Top 5 products by total sales
top_products = df4.groupby(['StockCode', 'Description'])['TotalSales'].sum().sort_values(ascending=False).head(5)

print(top_products)

# Plot percentage contribution for top 5 products
top5_products = top_products / top_products.sum() * 100
top5_products.plot(kind='bar', figsize=(10,6), color='lightgreen')
plt.title('Top 5 Products by Sales Percentage')
plt.ylabel('Percentage of Total Product Sales')
plt.show()
```

*Visual: Bar chart showing percentage sales contribution of top 5 products, with "Postage" around 2%*   

---

### 4. Customer Segmentation (RFM Analysis)

**Finding:**  
Customers segmented by Recency, Frequency, and Monetary (RFM) scores reveal valuable "gold customers" with high scores, who contribute significantly to revenue.

**Code and Visualization:**

```python
import pandas as pd

# Define current date as one day after max invoice date
current_date = df4['InvoiceDate'].max() + pd.Timedelta(days=1)

# Calculate RFM metrics
rfm = df4.groupby('CustomerID').agg({
    'InvoiceDate': lambda x: (current_date - x.max()).days,
    'InvoiceNo': 'nunique',
    'TotalSales': 'sum'
}).reset_index()

rfm.columns = ['CustomerID', 'Recency', 'Frequency', 'Monetary']

# Create quartiles for RFM
rfm['R_Quartile'] = pd.qcut(rfm['Recency'], 4, labels=[4,3,2,1])
rfm['F_Quartile'] = pd.qcut(rfm['Frequency'], 4, labels=[1,2,3,4])
rfm['M_Quartile'] = pd.qcut(rfm['Monetary'], 4, labels=[1,2,3,4])

# Calculate RFM score
rfm['RFM_Score'] = rfm[['R_Quartile', 'F_Quartile', 'M_Quartile']].sum(axis=1)

# Sort by RFM score descending
rfm_sorted = rfm.sort_values(by='RFM_Score', ascending=False)
print(rfm_sorted.head())
```

*Visual: Table output showing customers with highest RFM scores (valuable customers)*   

---

### 5. Customer Churn Indicators

**Finding:**  
Customers with Recency greater than 90 days are likely churned. Identifying these customers enables targeted retention efforts such as promotional offers.

**Code and Visualization:**

```python
# Define churn threshold
churn_threshold = 90

# Classify churned customers
rfm['Churn'] = rfm['Recency'] > churn_threshold

# Plot churn distribution histogram
plt.figure(figsize=(8,5))
rfm['Recency'].hist(bins=50)
plt.axvline(churn_threshold, color='red', linestyle='dashed', linewidth=2)
plt.title('Recency Distribution with Churn Threshold')
plt.xlabel('Days Since Last Purchase (Recency)')
plt.ylabel('Number of Customers')
plt.show()

# Plot count of churned vs active customers
rfm['Churn'].value_counts().plot(kind='bar', color=['green', 'red'])
plt.title('Customer Churn Distribution')
plt.xlabel('Churned')
plt.ylabel('Number of Customers')
plt.show()
```

*Visuals: Histogram of Recency with 90-day threshold and bar chart of churned vs active customers*    

---

### Project Outcome

This project not only provides actionable business insights into sales trends and customer behavior but also demonstrates skills in:

*   End-to-end data analysis pipeline development.
*   Advanced data cleaning and feature engineering.
*   Statistical analysis and data visualization.
*   Customer segmentation and churn prediction.

---

This compilation of findings with their corresponding code and visualizations provides a clear, practical understanding of how the insights were derived in the project.
