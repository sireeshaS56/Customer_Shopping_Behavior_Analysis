# Customer Shopping Behavior Analysis

## 📌 Project Overview

This project analyzes customer shopping behavior to identify purchasing patterns, customer segments, product performance, discount usage, subscription behavior, and revenue trends.

The project follows an end-to-end data analytics workflow:

**Raw Data → Python Data Cleaning & Transformation → PostgreSQL → SQL Analysis → Power BI Dashboard**

The goal is to transform raw customer shopping data into meaningful business insights that can support decisions related to customer retention, product strategy, promotions, subscriptions, and sales performance.

---

## 📂 Project Files

| File                                         | Description                                                                  |
| -------------------------------------------- | ---------------------------------------------------------------------------- |
| `customer_shopping_behavior.csv`             | Raw customer shopping behavior dataset                                       |
| `Customer_Shopping_Behaviour_Analysis.ipynb` | Python notebook used for data cleaning, transformation, and database loading |
| `customer_behaviour_analysis.sql`            | SQL queries used for business analysis                                       |
| `Customer_behaviour_analysis_dashboard.pbix` | Power BI dashboard for interactive visualization                             |
| `README.md`                                  | Project documentation                                                        |

---

## 📊 Dataset

The dataset contains **3,900 customer purchase records** with information covering customer demographics, purchases, products, reviews, subscriptions, shipping, discounts, payment methods, and purchasing frequency.

### Original Dataset Columns

* Customer ID
* Age
* Gender
* Item Purchased
* Category
* Purchase Amount (USD)
* Location
* Size
* Color
* Season
* Review Rating
* Subscription Status
* Shipping Type
* Discount Applied
* Promo Code Used
* Previous Purchases
* Payment Method
* Frequency of Purchases

The dataset contains:

* **3,900 customers/records**
* **25 different products**
* **4 product categories**
* **50 locations**
* **4 seasons**
* **6 shipping types**
* **6 payment methods**
* **7 purchase-frequency options**

---

# 🔧 Tools & Technologies

The project uses the following technologies:

### Python

Used for:

* Data loading
* Data inspection
* Missing-value handling
* Data cleaning
* Feature engineering
* Data transformation
* PostgreSQL integration

### Pandas

Used for manipulating and transforming the customer dataset.

### PostgreSQL

Used to store the cleaned customer data and perform SQL-based business analysis.

### SQL

Used to answer business questions and extract actionable insights from the customer data.

### Power BI

Used to build an interactive dashboard and visualize customer behavior and sales performance.

### Jupyter Notebook

Used as the development environment for the Python-based data preparation workflow.

---

# 🐍 Python Data Preparation

The Python notebook performs the following steps.

## 1. Load the Dataset

The CSV file is loaded into a Pandas DataFrame for analysis.

```python
import pandas as pd

df = pd.read_csv("customer_shopping_behavior.csv")
```

## 2. Explore the Dataset

Initial analysis is performed using:

```python
df.head()
df.info()
df.describe()
df.isnull().sum()
```

This helps understand the structure, data types, statistical distribution, and missing values.

## 3. Handle Missing Values

Missing values in the `Review Rating` column are handled using the median review rating within each product category.

```python
df['Review Rating'] = df.groupby('Category')['Review Rating'].transform(
    lambda x: x.fillna(x.median())
)
```

## 4. Standardize Column Names

Column names are converted to lowercase and spaces are replaced with underscores.

```python
df.columns = df.columns.str.lower()
df.columns = df.columns.str.replace(' ', '_')
```

The purchase amount column is then renamed:

```python
df = df.rename(columns={
    'purchase_amount_(usd)': 'purchase_amount'
})
```

## 5. Create Age Groups

Customers are divided into four age groups using quartiles:

* Young Adult
* Adult
* Middle-aged
* Senior

```python
labels = ['Young Adult', 'Adult', 'Middle-aged', 'Senior']

df['age_group'] = pd.qcut(
    df['age'],
    q=4,
    labels=labels
)
```

## 6. Convert Purchase Frequency

Purchase-frequency categories are converted into approximate numbers of days.

| Frequency      | Days |
| -------------- | ---: |
| Weekly         |    7 |
| Fortnightly    |   14 |
| Bi-Weekly      |   14 |
| Monthly        |   30 |
| Quarterly      |   90 |
| Every 3 Months |   90 |
| Annually       |  365 |

A new column called `purchase_frequency_days` is created.

## 7. Remove Redundant Column

The notebook checks whether `Discount Applied` and `Promo Code Used` contain the same information.

After confirming the values match, `Promo Code Used` is removed to avoid redundancy.

## 8. Load Data into PostgreSQL

The cleaned DataFrame is connected to PostgreSQL using SQLAlchemy and Psycopg2.

The cleaned dataset is stored in a PostgreSQL table named:

```text
customer
```

---

# 🗄️ SQL Business Analysis

After loading the cleaned data into PostgreSQL, SQL queries are used to answer important business questions.

The project contains **10 analytical questions**.

## Business Questions

### 1. Revenue by Gender

Compare the total revenue generated by male and female customers.

```sql
SELECT gender, SUM(purchase_amount) AS revenue
FROM customer
GROUP BY gender;
```

### 2. Discount Users with Above-Average Spending

Identify customers who used a discount but still spent more than the overall average purchase amount.

### 3. Top 5 Products by Average Review Rating

Identify the five products with the highest average customer review rating.

### 4. Shipping Type Comparison

Compare the average purchase amount between:

* Express
* Standard

shipping methods.

### 5. Subscriber vs Non-Subscriber Spending

Compare subscribers and non-subscribers based on:

* Number of customers
* Average purchase amount
* Total revenue

### 6. Products with Highest Discount Usage

Identify the five products with the highest percentage of purchases where a discount was applied.

### 7. Customer Segmentation

Customers are segmented based on previous purchases:

| Previous Purchases | Segment   |
| ------------------ | --------- |
| 1                  | New       |
| 2–10               | Returning |
| More than 10       | Loyal     |

The analysis then counts customers in each segment.

### 8. Top Products Within Each Category

Using a window function, the analysis identifies the top three most purchased products within each product category.

### 9. Repeat Buyers and Subscription Behavior

Repeat buyers are defined as customers with more than five previous purchases.

The analysis compares their subscription status to determine whether repeat buyers are more likely to subscribe.

### 10. Revenue by Age Group

Total revenue is calculated for each customer age group and ranked from highest to lowest.

---

# 📈 Power BI Dashboard

The cleaned and analyzed customer data is visualized using Power BI.

The dashboard contains interactive visuals designed to provide a high-level view of customer behavior and sales performance.

### Dashboard Components

The Power BI report includes:

* KPI cards
* Subscription-status analysis
* Revenue by category
* Sales by category
* Customer filtering/slicers
* Category/product analysis
* Bar-chart based comparisons

### Interactive Filters

The dashboard includes slicers that allow the user to explore the data dynamically and analyze customer behavior across different dimensions.

---

# 🔄 End-to-End Workflow

```text
                 Raw CSV Dataset
                       │
                       ▼
              Python / Pandas
                       │
          ┌────────────┴────────────┐
          │                         │
     Data Cleaning            Feature Engineering
          │                         │
          └────────────┬────────────┘
                       │
                       ▼
                PostgreSQL
                 customer table
                       │
                       ▼
                 SQL Analysis
                       │
                       ▼
              Business Insights
                       │
                       ▼
                  Power BI
                   Dashboard
```

---

# 💡 Key Analytical Areas

The project focuses on several important areas of customer behavior:

### Customer Demographics

* Age
* Gender
* Location
* Age group

### Product Performance

* Products purchased
* Product categories
* Review ratings
* Top-performing products

### Purchasing Behavior

* Purchase amount
* Previous purchases
* Purchase frequency
* Payment method

### Marketing & Promotions

* Discounts
* Promo-code usage
* Discount rates by product

### Customer Loyalty

* New customers
* Returning customers
* Loyal customers
* Repeat buyers

### Subscription Behavior

* Subscribers vs non-subscribers
* Spending behavior
* Revenue contribution
* Repeat-buyer subscription behavior

### Delivery & Fulfillment

* Shipping type
* Average purchase amount by shipping method

---

# 🎯 Project Objectives

The main objectives of this project are to:

1. Understand customer purchasing behavior.
2. Analyze revenue across different customer groups.
3. Identify high-performing products and categories.
4. Understand the impact and usage of discounts.
5. Compare subscriber and non-subscriber purchasing behavior.
6. Segment customers according to purchase history.
7. Analyze repeat-customer behavior.
8. Understand revenue contribution across age groups.
9. Compare shipping methods based on average purchase amount.
10. Build an interactive dashboard for business analysis.

---

# 🚀 How to Run the Project

## Step 1: Clone the Repository

```bash
git clone <your-github-repository-url>
cd <repository-name>
```

## Step 2: Install Python Dependencies

```bash
pip install pandas psycopg2-binary sqlalchemy
```

## Step 3: Run the Jupyter Notebook

Open:

```text
Customer_Shopping_Behaviour_Analysis.ipynb
```

Make sure the CSV file is available in the same project directory:

```text
customer_shopping_behavior.csv
```

Run the notebook cells sequentially to clean and transform the data.

## Step 4: Configure PostgreSQL

Create a PostgreSQL database for the project and update the database connection details in the notebook.

The notebook creates a connection using SQLAlchemy and loads the cleaned data into the `customer` table.

> **Security Note:** Database credentials should not be committed to GitHub. Store them in environment variables or a `.env` file instead.

## Step 5: Run SQL Analysis

Open:

```text
customer_behaviour_analysis.sql
```

Run the queries against the PostgreSQL `customer` table.

## Step 6: Open the Power BI Dashboard

Open:

```text
Customer_behaviour_analysis_dashboard.pbix
```

If the database connection needs to be refreshed, update the data-source connection to your local PostgreSQL environment.

---

# 📁 Suggested Repository Structure

```text
Customer-Shopping-Behavior-Analysis/
│
├── customer_shopping_behavior.csv
├── Customer_Shopping_Behaviour_Analysis.ipynb
├── customer_behaviour_analysis.sql
├── Customer_behaviour_analysis_dashboard.pbix
└── README.md
```

---

# 📌 Conclusion

This project demonstrates an end-to-end customer analytics workflow, starting with raw shopping data and progressing through data cleaning, feature engineering, database storage, SQL analysis, and interactive Power BI visualization.

By combining **Python, PostgreSQL, SQL, and Power BI**, the project provides a structured approach to understanding customer behavior, purchasing patterns, product performance, discounts, subscriptions, customer loyalty, and revenue distribution.

---

## 🛠️ Technologies Used

`Python` · `Pandas` · `Jupyter Notebook` · `PostgreSQL` · `SQL` · `SQLAlchemy` · `Psycopg2` · `Power BI`

