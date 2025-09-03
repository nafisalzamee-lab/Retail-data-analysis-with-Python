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
