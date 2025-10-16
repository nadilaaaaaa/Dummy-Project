# 🍴 Balaji Fast Food Sales — Data Cleaning & Dashboard Analytics
📥 [Download the original dataset here]: [Restaurant sales report – Kaggle](https://www.kaggle.com/datasets/rajatsurana979/fast-food-sales-report)

---

## 📖 Project Overview  

This project is designed to enhance my skills in ***data cleaning, preprocessing, exploration, and dashboard creation***. The dataset used in this project is a ***secondary data obtained from Kaggle***. The workflow begins with a comprehensive **data cleaning process**, which includes handling missing values, duplicates, outliers, and inconsistent data formats to ensure the dataset is accurate, complete, and analysis-ready.  

After cleaning, the dataset is connected to Google Looker Studio (via Google Sheets) to perform data analysis and create an interactive dashboard. The dashboard highlights key business metrics such as total sales, top-selling items, customer behavior by time of sale, and payment preferences, providing a clear overview of the company’s sales performance.

This end-to-end project demonstrates proficiency in Python (Pandas, NumPy) for data preprocessing and Looker Studio for visualization, showcasing my ability to clean, transform, and analyze data effectively through interactive dashboards.

---

## 📂 Dataset Overview

**File Name:** `Balaji Fast Food Sales.csv`  
**Number of Records:** 1,000 rows × 10 columns  

| Column | Description |
|--------|-------------|
| `order_id` | Unique identifier for each transaction |
| `date` | Date of the transaction (cleaned and standardized) |
| `item_name` | Name of the food or beverage item |
| `item_type` | Type of item (Fastfood, Beverages) |
| `item_price` | Price per item |
| `quantity` | Quantity sold |
| `transaction_amount` | Total amount of transaction |
| `transaction_type` | Mode of payment (Cash, Online, Unknown) |
| `received_by` | Cashier (Mr/Mrs) |
| `time_of_sale` | Time of sale (Morning, Afternoon, Evening, etc.) |

---
## ⚙️ Tools & Libraries  
Here are the main tools used for analyzing and visualizing the data:
| Tools | Purpose |
|------|----------|
| 🐍 Google Colab | Main programming language |
| 🧾 Looker Studio | Interactive dashboard |

For data cleaning and preprocessing in Google Colab, the following Python libraries were used:
| Libraries | Purpose |
|------|----------|
| 📊 Pandas | Data cleaning & manipulation |
| 🔢 NumPy | Numerical operations |

---
## 🧹 Data Cleaning Workflow  
The following steps were executed in **Google Colab** inside  
📓 [`Dummy Project Balaji Sales Report.ipynb`](https://colab.research.google.com/drive/1BLbDzVChMvMUohwSzLznk-IWNWGx7SPg?usp=sharing)
### 1. Import Libraries
```python
import pandas as pd
import numpy as np
```
### 2. Load the Dataset
```python
df = pd.read_csv('Balaji Fast Food Sales.csv')
df
```
### 3. Data Overview
```python
df.shape
df.info()
df.describe()
df.isnull().sum()
```
### 4. Handle Missing Values
```python
df['transaction_type'].fillna('Unknown', inplace=True)
value_counts = df['transaction_type'].value_counts()
print(value_counts)
```
### 5. Fix Date Format Inconsistency  
```python
df['date_cleaned'] = pd.to_datetime(df['date'], errors='coerce', dayfirst=True)
print(df['date_cleaned'])

mask_nat = df['date_cleaned'].isna()
df.loc[mask_nat, 'date_cleaned'] = pd.to_datetime(df.loc[mask_nat, 'date'], errors='coerce', dayfirst=False)
df['date'] = df['date_cleaned']
df.drop(columns=['date_cleaned'], inplace=True)
print(df['date'])
```
### 6. Clean Text Columns  
```python
text_cols = ['item_name', 'item_type', 'received_by', 'time_of_sale', 'transaction_type']
for col in text_cols:
    df[col] = df[col].astype(str).str.lower().str.strip().str.replace('.', '', regex=False)
    print(df[col])
```
### 7. Remove Duplicates
```python
df.drop_duplicates(inplace=True)
```
### 8. Ensure Transaction Amounts Are Correct
```python
df['calculated_amount'] = df['item_price'] * df['quantity']
inconsistent_rows_mask = df['transaction_amount'] != df['calculated_amount']
df.loc[inconsistent_rows_mask, 'transaction_amount'] = df.loc[inconsistent_rows_mask, 'calculated_amount']
df.drop(columns=['calculated_amount'], inplace=True)
```
### 9. Outlier Detection (IQR Method)
```python
numeric_cols = ['item_price', 'quantity', 'transaction_amount']
for col in numeric_cols:
    Q1 = df[col].quantile(0.25)
    Q3 = df[col].quantile(0.75)
    IQR = Q3 - Q1

    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR

    outliers = df[(df[col] < lower_bound) | (df[col] > upper_bound)]
    outlier_count = len(outliers)

    percentage = (outlier_count / len(df)) * 100

    print(f"\nKolom: {col}")
    print(f"  Batas Atas (Upper Bound): {upper_bound:.2f}")
    print(f"  Jumlah Outlier: {outlier_count} baris")
    print(f"  Persentase Outlier: {percentage:.2f}%")

    if outlier_count > 0:
        print(f"  Nilai Outlier Minimum: {outliers[col].min()}")
        print(f"  Nilai Outlier Maksimum: {outliers[col].max()}")
```
### 10. Recheck Dataset After Cleaning
```python
df.info()
df.describe()
df.isnull().sum()
```
### 11. Export Cleaned Dataset to Google Drive
```python
!pip install gspread gspread-dataframe
import pandas as pd
from google.colab import drive
drive.mount('/content/drive')
file_path = '/content/drive/MyDrive/Balaji_Sales_Cleaned_For_Looker.csv'
df.to_csv(file_path, index=False)
```
---
## 📊 Dashboard 
After exporting the cleaned dataset to Google Drive (linked directly to Google Sheets), I connected the data to Google Looker Studio in order to build an interactive dashboard for comprehensive visualization and analytical insights.
🔗***Interactive Dashboard: https://lookerstudio.google.com/reporting/f140fb89-1e5b-444d-8145-1973dd56614f***
<img width="900" height="675" alt="image" src="https://github.com/user-attachments/assets/96d9cfe7-cbf4-4cb6-ab1e-46e708bbf89a" />

