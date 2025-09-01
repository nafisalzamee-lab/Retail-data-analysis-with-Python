# Retail-data-analysis-with-Python
Overview
This project demonstrates an end-to-end data analysis workflow on a real-world e-commerce dataset, focusing on exploratory data analysis (EDA), customer segmentation, and performance optimization for large datasets. Utilizing a dataset of half a million sales transactions from a UK-based online retailer, the project uncovers key business insights and showcases proficiency in handling big data challenges. A significant aspect of this project involves scaling the analysis to 32 million records using the FireDucks library, highlighting its efficiency compared to traditional Pandas for large-scale data processing s s .
Key Features & Methodologies
Data Acquisition & Loading:
Sourced a dataset of approximately 500,000 sales transactions from a UK online retailer, including details such as Invoice Number, Stock Code, Description, Quantity, Invoice Date, Unit Price, and Customer ID s s .
Implemented efficient data loading into a Pandas DataFrame, with explicit type specification for optimization due to the dataset's size s s .
Robust Data Cleaning & Preprocessing:
Missing Value Imputation: Addressed missing Description values by identifying the most frequent description for each Stock Code and using it for imputation, ensuring data integrity s s .
Handling Invalid Entries: Cleaned data by removing records with negative Quantity or Unit Price, which were identified as errors through business context analysis s s .
Outlier Consideration: Explored extreme Quantity values (e.g., 99th percentile and above) and made informed decisions on retaining them based on potential business scenarios (e.g., wholesale orders), demonstrating practical data handling judgment s s .
Feature Engineering for Enhanced Analysis:
Total Sales Calculation: Created a new Total Sales column by multiplying Quantity and Unit Price, essential for revenue analysis s s .
Temporal Feature Extraction: Extracted the Month from Invoice Date to facilitate time-based trend analysis, enabling insights into monthly sales patterns s .
Exploratory Data Analysis (EDA) & Visualization:
Monthly Sales Trend Analysis: Visualized monthly sales trends using line and bar charts, identifying significant patterns such as the "hockey curve" effect towards the end of the year, likely due to holiday seasons or sales incentives s s .
Geographical Sales Distribution: Analyzed and visualized top countries by total sales, including percentage contributions, revealing insights into market dependency (e.g., high reliance on UK sales) and informing diversification strategies s s .
Product Performance Analysis: Identified top-selling products by Stock Code and Description, assessing product diversification and sales concentration across the product catalog s s .
Advanced Customer Analytics (RFM & Churn Analysis):
RFM (Recency, Frequency, Monetary) Analysis:
Calculated Recency (days since last purchase), Frequency (number of transactions), and Monetary (total sales value) for each customer s s .
Segmented customers into quartiles (tiers) based on RFM scores, allowing for identification of valuable customers (e.g., "gold customers") who can be targeted with specific marketing campaigns s s .
Customer Churn Analysis:
Determined customer churn likelihood based on Recency values, setting a threshold (e.g., 90 days) to classify churned customers s s .
Visualized the distribution of recency values to identify the number of churned customers, providing actionable insights for retention strategies (e.g., promotional offers, coupons) s s .
Large-Scale Data Processing with FireDucks:
Demonstrated the capability to analyze 32 million records by synthetically expanding the original dataset s s .
Showcased FireDucks as a high-performance, Pandas-compatible library, achieving significant speed improvements (e.g., 167 seconds with Pandas vs. 90 seconds with FireDucks for the same operations) by simply changing an import statement s s .
Highlighted FireDucks' advantages in sustainability, economics, multi-threading, JIT compilation, and CPU acceleration for large datasets s s .
Tools & Technologies
Python: Primary programming language.
Pandas: For data manipulation and analysis.
FireDucks: For accelerated large-scale data processing.
Matplotlib/Seaborn: For data visualization.
Google Colab: Cloud-based Jupyter environment with AI assistance.
Project Outcome
This project not only provides actionable business insights into sales trends and customer behavior but also serves as a strong demonstration of skills in:
End-to-end data analysis pipeline development.
Advanced data cleaning and feature engineering.
Statistical analysis and data visualization.
Customer segmentation and churn prediction.
Optimizing data processing for large datasets using high-performance libraries like FireDucks.
This project is a testament to the ability to derive meaningful insights from complex datasets and apply modern data tools for efficient and scalable analysis.
