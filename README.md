# Large-Scale E-commerce Sales Data Analysis with Python Pandas

This project description outlines a comprehensive data analysis project, ideal for showcasing skills in large-scale data processing, exploratory data analysis (EDA), and customer analytics.

---

## Project Title: Large-Scale E-commerce Sales Data Analysis with Python Pandas 

### Overview

This project demonstrates an end-to-end data analysis workflow on a real-world e-commerce dataset, focusing on **exploratory data analysis (EDA)**, **customer segmentation**, and **performance optimization** for large datasets. Utilizing a dataset of half a million sales transactions from a UK-based online retailer, the project uncovers key business insights and showcases proficiency in handling big data challenges. A significant aspect of this project involves scaling the analysis utilizing efficiency of Pandas for large-scale data processing. 

---

### Key Features & Methodologies

---

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
```python

### 2. Robust Data Cleaning & Preprocessing

* **Missing Value Imputation**: Replaced missing `Description` values by finding the most frequent description per `StockCode` and using it for imputation.

```python
# Identify missing descriptions
missing_desc = df1['Description'].isnull().sum()

# Get most frequent description per StockCode
most_frequent = df1.groupby('StockCode')['Description'] \.agg(lambda x: x.mode().iloc[0] if not x.mode().empty else None) \.reset_index() \.rename(columns={'Description': 'FrequentDescription'})

# Merge with original DataFrame
df2 = df1.merge(most_frequent, on='StockCode', how='left')

# Replace Description with FrequentDescription
df2['Description'] = df2['FrequentDescription']

# Drop helper column
df2.drop(columns=['FrequentDescription'], inplace=True)

# Drop rows with any remaining null descriptions
df2.dropna(subset=['Description'], inplace=True)
