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
df1 = pd.read_excel('Online Retail.xlsx',dtype={'InvoiceNo':'string','StockCode':'string','Description':'string','Country':'string'})
df1.head(3)
print(df1.shape)
df1.info()
```

### 2. Robust Data Cleaning & Preprocessing

* **Missing Value Imputation**: Replaced missing `Description` values by finding the most frequent description per `StockCode` and using it for imputation.

```python
# Identifying missing descriptions
df1[df1['Description'].isnull()].head()

# Getting most frequent description per StockCode using value_counts()
most_freq=df1[['StockCode','Description']].value_counts().reset_index()
most_freq

# Merging with original DataFrame using .merge()
most_freq.columns=['StockCode','freq_Description','Count']
df2=df1.merge(most_freq,on='StockCode',how='left')
df2.head(3)

# Replacing  Description with FrequentDescription to get rid of the missing descriotions

df2['Description']=df2['freq_Description']
df2.isnull().sum()

# Dropping helper columns from the main dataframe using dropna()
df2.drop(columns=['freq_Description','Count'],inplace=True)

# Dropping rows with any remaining null descriptions
df2.dropna(subset=['Description'], inplace=True)
df2.isnull().sum()
```

* **Handling Invalid Values**: Removed records with negative `Quantity` or `Unit Price` values, identified as errors.

```python
# Removing negative or zero quantities & prices
df3=df2[(df2['Quantity']>0) & (df2['UnitPrice']>0)]
df3.describe()
```

* **Outlier Consideration**: Examined extreme `Quantity` values and retained them based on business context (e.g., wholesale orders).

```python
# Checking 99th percentile for Quantity
df3.Quantity.quantile(.9999)
```

### 3. Feature Engineering for Enhanced Analysis

* Created a new `TotalSales` column by multiplying `Quantity` and `UnitPrice`.

```python
df4 = df3.copy()
df4['Total Sales']=df4['Quantity']*df4['UnitPrice']
```

* Extracted the `Month` from `InvoiceDate` for time-based trend analysis.

```python
# Derived the number of month for ''Month' column from invoice date applying Pandas Date time accessor .dt.month on it.
df4['Month']=df4['InvoiceDate'].dt.month
df4.sample(3)
```

### 4. Exploratory Data Analysis (EDA) & Visualization

* **Monthly Sales Trend Analysis**: Grouped sales by month and plotted trends.

```python
# Calculating monthly sales using groupby() function
monthly_sales=df4.groupby('Month')['Total Sales'].sum()
monthly_sales

# Plotting  line chart using plot() function
monthly_sales.plot(kind='line',title='Monthly total Sales Trend',marker='o',grid=True)
plt.xlabel('Month')
plt.ylabel('Total Sales')
plt.show()
```

* **Geographical Sales Distribution**: Analyzed top countries by total sales and their percentage contributions.

```python
#Using groupby() countries & then using sort_values() function to get the top 5 country
top_5_country=df4.groupby('Country')['Total Sales'].sum().sort_values(ascending=False).head(5)
top_5_country

# Calculating percentage contribution of each country to total sales
country_sales=df4.groupby('Country')['Total Sales'].sum()
total_sales=country_sales.sum()
top_5_country=df4.groupby('Country')['Total Sales'].sum().sort_values(ascending=False).head(5)
percentage=(top_5_country/total_sales)*100

# Plotting bar chart to show percentage contribution to total sales by Country
plt.figure(figsize=(10,6))
plt.barh(percentage.index,percentage.values)
plt.xlabel('Percentage of Total Sales')
plt.ylabel('Country')
plt.title('Top 5 Countries by Percentage Contribution to Total Sales')
plt.show()
```

* **Product Performance Analysis**: Identified top-selling products by `StockCode` and `Description`.

```python
#Finding out Top 5 products based on total sales grouping by 'StockCode' & then using sort_values() function to get the top 5 country
product_wisesales=df4.groupby('StockCode')['Total Sales'].sum()
top_5_products=df4.groupby('StockCode')['Total Sales'].sum().sort_values(ascending=False).head(5)

#Printing the description of the top 5 products using foor loop & f""
for stock_code in top_5_products.index:
  description=df4[df4.StockCode==stock_code].Description.iloc[0]
  print(f"{stock_code}==>{description}")

#Plottin Bar chart to show  top 5 products by Percentage of contribution to total Sales
plt.figure(figsize=(10,6))
bars=plt.barh(percentages.index,percentages.values)
plt.xlabel('Percentage of Total Sales')
plt.ylabel('Stock Code')
plt.title('Top 5 Products by Percentage Contribution to Total Sales')
plt.grid(axis='x')


# Adding percentage labels to the bars

for bar,percentage in zip(bars,percentages):
  plt.text(bar.get_width()+0.5,bar.get_y()+bar.get_height()/2,f'{percentage:.1f}%',va='center')

plt.show()
```

### 5. Advanced Customer Analytics (RFM & Churn Analysis)

* **RFM Analysis**: Calculated Recency, Frequency, and Monetary values per customer.

```python
#max invoice date using max() function
df4.InvoiceDate.max()

# Defining current date as one day after max invoice date while using Timedelta() class
current_date=df4['InvoiceDate'].max() + pd.Timedelta(days=1)


#Calculating Recency, Frequency, and Monetary values per customer using agga(),lamda x() & groupby() function
rfm=df4.groupby('CustomerID').agg({'InvoiceDate':lambda x:(current_date - x.max()).days,
                                   'InvoiceNo': 'count',
                                   'Total Sales': 'sum'})
rfm.columns=['Recency','Frequency','Monetary']
rfm.head()
```

* Created quartiles for RFM scores and calculated an overall RFM score.It helped to segment customers.

```python
rfm['R_Segment']=pd.qcut(rfm['Recency'],4,labels=[4,3,2,1])
rfm['F_Segment']=pd.qcut(rfm['Frequency'],4,labels=[1,2,3,4])
rfm['M_Segment']=pd.qcut(rfm['Monetary'],4,labels=[1,2,3,4])
rfm['RFM_Score']=rfm[['R_Segment','F_Segment','M_Segment']].sum(axis=1)

rfm.sample(5)

#Customers with highest RFM Score
rfm.sort_values('RFM_Score',ascending=False)
```

*   **Customer Churn Analysis**: Classified customers as churned if `Recency` exceeded 90 days.

```python
#Calculating the number of days when a customer purchased for the last time from the company
customer_last_purchase=(current_date-customer_last_purchase).dt.days
customer_last_purchase.head(4)

# defining churn thresh hold (90 days without any purchase)
churn_thresh_hold= 90
churned_customers=customer_last_purchase[customer_last_purchase>churn_thresh_hold]
churned_customers.head(4)

#Getting the total number of churned customers
print("Number of Churned Customers: ",len(churned_customers))

# Visualize churn distribution
plt.figure(figsize=(10,6))
plt.hist(customer_last_purchase,bins=50,color='red',alpha=0.7)
plt.axvline(churn_thresh_hold,color='black',linestyle='dashed',linewidth=2)
plt.title("Customer Churn Distribution")
plt.xlabel('Days since last purchase')
plt.ylabel('No. of Customers')
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

```

*Visual: Line chart showing monthly sales increasing sharply towards months 10, 11, and 12*   

---

### 2. Geographical Sales Dependency

**Finding:**  
The UK contributes approximately 84.6% of total sales, indicating high market dependency. This suggests potential business risk and opportunity for diversification.

**Code and Visualization:**

```python

```

*Visual: Horizontal bar chart showing UK dominating sales percentage, followed by Netherlands and others*   

---

### 3. Product Sales Diversification

**Finding:**  
The top-selling product ("Postage") accounts for only about 1.9% to 2% of total product sales, indicating good product diversification with no excessive dependency on a single product.

**Code and Visualization:**

```python
```

*Visual: Bar chart showing percentage sales contribution of top 5 products, with "Postage" around 2%*   

---

### 4. Customer Segmentation (RFM Analysis)

**Finding:**  
Customers segmented by Recency, Frequency, and Monetary (RFM) scores reveal valuable "gold customers" with high scores, who contribute significantly to revenue.

**Code and Visualization:**

```python
```

*Visual: Table output showing customers with highest RFM scores (valuable customers)*   

---

### 5. Customer Churn Indicators

**Finding:**  
Customers with Recency greater than 90 days are likely churned. Identifying these customers enables targeted retention efforts such as promotional offers.

**Code and Visualization:**

```python
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
