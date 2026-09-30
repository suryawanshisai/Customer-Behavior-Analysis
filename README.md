# Customer-Behavior-Analysis
An end-to-end Data Analytics project using Python, PostgreSQL, SQL and Power BI.

![Customer Behavior Dashboard](images/customer_behavior_dashboard.png)

# Customer Behavior Analytics

An end-to-end **Data Analytics project** that analyzes customer shopping behavior using **Python, PostgreSQL, SQL, and Power BI** to identify customer trends, purchasing patterns, category performance, and customer engagement insights.

---

## 📌 Project Overview

Customer shopping data contains valuable information about customer demographics, purchasing behavior, product preferences, subscriptions, discounts, payment methods, shipping preferences, and purchase frequency.

The objective of this project is to analyze customer behavior and transform raw data into meaningful business insights using an end-to-end data analytics workflow.

The project covers:

* Data loading using Python
* Exploratory Data Analysis (EDA)
* Data cleaning and transformation
* Feature engineering
* PostgreSQL database integration
* SQL-based business analysis
* Power BI dashboard development
* Business insights and recommendations
* Project report preparation
* Project presentation using Gamma

---

## 🎯 Business Problem

A leading retail company wants to better understand its customers' shopping behavior in order to improve sales, customer satisfaction, and long-term customer engagement.

The management team is interested in understanding purchasing patterns across:

* Customer demographics
* Product categories
* Discounts and promotions
* Customer reviews
* Seasons
* Subscription status
* Payment methods
* Shipping preferences
* Purchase frequency

### Business Question

> **How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?**

---

## 📊 Dataset

The dataset contains **3,900 customer records** and **18 original columns** related to customer shopping behavior.

### Dataset Columns

| Column                 | Description                             |
| ---------------------- | --------------------------------------- |
| Customer ID            | Unique identifier for each customer     |
| Age                    | Customer age                            |
| Gender                 | Customer gender                         |
| Item Purchased         | Product purchased by the customer       |
| Category               | Product category                        |
| Purchase Amount (USD)  | Amount spent by the customer            |
| Location               | Customer location                       |
| Size                   | Product size                            |
| Color                  | Product color                           |
| Season                 | Purchase season                         |
| Review Rating          | Customer review rating                  |
| Subscription Status    | Whether the customer has a subscription |
| Shipping Type          | Shipping method selected                |
| Discount Applied       | Whether a discount was applied          |
| Promo Code Used        | Whether a promotional code was used     |
| Previous Purchases     | Number of previous purchases            |
| Payment Method         | Payment method used                     |
| Frequency of Purchases | Customer purchase frequency             |

### Dataset Summary

* **Rows:** 3,900
* **Original Columns:** 18
* **Product Categories:** 4
* **Products:** 25
* **Locations:** 50
* **Seasons:** 4
* **Payment Methods:** 6
* **Shipping Types:** 6
* **Purchase Frequencies:** 7

---

# 🛠️ Tools & Technologies

| Technology           | Purpose                                        |
| -------------------- | ---------------------------------------------- |
| **Python**           | Data loading, cleaning, transformation and EDA |
| **Pandas**           | Data manipulation and analysis                 |
| **Jupyter Notebook** | Python development and analysis                |
| **PostgreSQL**       | Database storage                               |
| **SQL**              | Business data analysis                         |
| **Power BI**         | Interactive dashboard and visualization        |
| **Gamma**            | Project presentation                           |
| **Git & GitHub**     | Version control and project showcase           |

### Python Libraries

```text
pandas
sqlalchemy
psycopg2-binary
```

---

# 🔄 Project Workflow

```text
Raw Dataset
     ↓
Data Loading using Python
     ↓
Exploratory Data Analysis
     ↓
Data Cleaning
     ↓
Feature Engineering
     ↓
Load Cleaned Data into PostgreSQL
     ↓
SQL Business Analysis
     ↓
Power BI Dashboard
     ↓
Business Insights
     ↓
Project Report
     ↓
Project Presentation using Gamma
```

---

# 1️⃣ Data Loading

The dataset was loaded into Python using the Pandas library.

```python
import pandas as pd

df = pd.read_csv("customer_shopping_behavior.csv")
```

The first few records were inspected using:

```python
df.head()
```

This helped understand the structure and values of the dataset.

---

# 2️⃣ Exploratory Data Analysis

Initial data exploration was performed to understand the dataset structure, data types, statistical information, and missing values.

### Dataset Information

```python
df.info()
```

The dataset contained:

* 3,900 records
* 18 columns
* 4 numerical columns
* 1 floating-point column
* 13 categorical columns

### Statistical Analysis

Summary statistics were generated using:

```python
df.describe(include="all")
```

This helped analyze:

* Mean
* Standard deviation
* Minimum and maximum values
* Quartiles
* Unique values
* Most frequent categories

---

# 3️⃣ Missing Value Analysis

Missing values were checked using:

```python
df.isnull().sum()
```

The analysis identified **37 missing values in the `Review Rating` column**.

All other columns contained no missing values.

---

# 4️⃣ Data Cleaning

The missing review ratings were handled using category-level median imputation.

```python
df["Review Rating"] = df.groupby("Category")["Review Rating"].transform(
    lambda x: x.fillna(x.median())
)
```

After cleaning, the dataset was checked again:

```python
df.isnull().sum()
```

The result showed that there were no remaining missing values.

---

# 5️⃣ Column Standardization

The column names were standardized to lowercase and snake_case format to improve readability and make SQL/database analysis easier.

```python
df.columns = df.columns.str.lower()

df.columns = df.columns.str.replace(" ", "_")

df = df.rename(
    columns={"purchase_amount_(usd)": "purchase_amount"}
)
```

For example:

```text
Purchase Amount (USD)
```

was converted to:

```text
purchase_amount
```

---

# 6️⃣ Feature Engineering

Two new analytical columns were created.

## Age Group

Customer ages were divided into four groups using quartile-based segmentation.

```python
labels = [
    "Young Adult",
    "Adult",
    "Middle-aged",
    "Senior"
]

df["age_group"] = pd.qcut(
    df["age"],
    q=4,
    labels=labels
)
```

This created the following age groups:

* Young Adult
* Adult
* Middle-aged
* Senior

---

## Purchase Frequency in Days

Purchase frequency categories were converted into numerical day intervals.

```python
frequency_mapping = {
    "Fortnightly": 14,
    "Weekly": 7,
    "Monthly": 30,
    "Quarterly": 90,
    "Bi-Weekly": 14,
    "Annually": 365,
    "Every 3 Months": 90
}

df["purchase_frequency_days"] = (
    df["frequency_of_purchases"].map(frequency_mapping)
)
```

This transformation makes purchase frequency easier to analyze numerically.

---

# 7️⃣ Redundant Column Analysis

The relationship between:

* `discount_applied`
* `promo_code_used`

was checked.

```python
(df["discount_applied"] == df["promo_code_used"]).all()
```

The result was:

```text
True
```

Since both columns contained the same information, `promo_code_used` was removed to avoid redundancy.

```python
df = df.drop("promo_code_used", axis=1)
```

---

# 8️⃣ PostgreSQL Database Integration

After data cleaning and transformation, the DataFrame was loaded into PostgreSQL.

### PostgreSQL Database

```text
Database: customer_behavior
Table: customer
Host: localhost
Port: 5432
```

Python was connected to PostgreSQL using **SQLAlchemy** and **psycopg2**.

```python
from sqlalchemy import create_engine

username = "postgres"
password = "YOUR_PASSWORD"
host = "localhost"
port = "5432"
database = "customer_behavior"

engine = create_engine(
    f"postgresql+psycopg2://{username}:{password}@{host}:{port}/{database}"
)

table_name = "customer"

df.to_sql(
    table_name,
    engine,
    if_exists="replace",
    index=False
)
```

The cleaned dataset was successfully loaded into the `customer` table.

> **Security Note:** Database passwords should never be uploaded to a public GitHub repository. Use environment variables or a local configuration file instead.

---

# 9️⃣ SQL Business Analysis

After loading the cleaned dataset into PostgreSQL, SQL queries were used to analyze customer behavior and identify business insights.

The SQL analysis focused on areas such as:

* Customer purchasing behavior
* Revenue by category
* Sales by category
* Customer age groups
* Subscription status
* Customer purchase frequency
* Product performance
* Customer reviews
* Discounts
* Payment methods
* Shipping types

The SQL queries are included in:

```text
sql/customer_behavior.sql
```

---

# 🔟 Power BI Dashboard

The cleaned and analyzed customer data was used to build an interactive Power BI dashboard.

## Dashboard Title

**Customer Behavior Dashboard**

### Key Performance Indicators

The dashboard displays:

* Number of Customers
* Average Purchase Amount
* Average Review Rating

### Dashboard Visualizations

The dashboard includes:

* % of Customers by Subscription Status
* Revenue by Category
* Sales by Category
* Revenue by Age Group
* Sales by Age Group

### Dashboard Filters

Users can interact with the dashboard using filters for:

* Subscription Status
* Gender
* Category
* Shipping Type

---

## 📸 Dashboard Preview

![Customer Behavior Dashboard](images/dashboard.png)

---

# 📈 Dashboard Results

Based on the analyzed dataset, the dashboard shows the following key metrics:

| KPI                      |  Value |
| ------------------------ | -----: |
| Number of Customers      |   3.9K |
| Average Purchase Amount  | $59.76 |
| Average Review Rating    |   3.75 |
| Subscribed Customers     |    27% |
| Non-Subscribed Customers |    73% |

### Category Analysis

The dashboard compares revenue and sales across:

* Clothing
* Accessories
* Footwear
* Outerwear

### Age Group Analysis

Customer revenue and sales are analyzed across:

* Young Adult
* Adult
* Middle-aged
* Senior

These visualizations help identify differences in purchasing activity and revenue contribution across customer segments.

---

# 💡 Business Insights

The project provides a visual and analytical view of customer shopping behavior.

Key areas identified through the analysis include:

### Customer Engagement

The dashboard shows the distribution of customers based on subscription status, helping understand the proportion of subscribed and non-subscribed customers.

### Category Performance

Revenue and sales are compared across different product categories to identify categories contributing to customer purchases.

### Customer Segmentation

Age-group analysis provides a way to compare purchasing behavior across different customer segments.

### Customer Experience

The average review rating provides an overview of customer feedback associated with purchases.

### Purchase Behavior

Purchase frequency, previous purchases, discounts, payment methods, and shipping types provide additional dimensions for understanding customer behavior.

---

# 📋 Project Deliverables

This project contains multiple deliverables covering the complete analytics workflow.

### 1. Python Analysis

Contains:

* Data loading
* EDA
* Data quality checks
* Missing-value treatment
* Data cleaning
* Feature engineering
* PostgreSQL connection

File:

```text
python/Customer_Shopping_Behavior_Analysis.ipynb
```

### 2. SQL Analysis

Contains SQL queries used for business analysis.

File:

```text
sql/customer_behavior.sql
```

### 3. Power BI Dashboard

Interactive dashboard containing KPIs, charts, and filters.

File:

```text
powerbi/customer_behavior_dashboard.pbix
```

### 4. Project Report

Detailed project report containing the analysis, findings, and recommendations.

File:

```text
report/Customer_Behavior_Analytics_Report.pdf
```

### 5. Project Presentation

Presentation created using Gamma to communicate the project, analysis, insights, and recommendations.

File:

```text
presentation/Customer_Behavior_Analytics_Presentation.pdf
```

---

# 📁 Repository Structure

```text
Customer-Behavior-Analytics/
│
├── 📁 data/
│   └── customer_shopping_behavior.csv
│
├── 📁 python/
│   └── customer_behavior_analysis.ipynb
│
├── 📁 sql/
│   └── customer_behavior_queries.sql
│
├── 📁 powerbi/
│   └── customer_behavior_dashboard.pbix
│
├── 📁 report/
│   └── Customer_Behavior_Analytics_Report.pdf
│
├── 📁 presentation/
│   └── Customer_Behavior_Analytics_Presentation.pdf
│
├── 📁 images/
│   ├── business_problem.png
│   └── dashboard.png
│
├── requirements.txt
└── README.md
```

---

# 🖼️ Business Problem

The project was developed around a retail customer behavior business problem.

![Business Problem Statement](images/business_problem.png)

---

# ▶️ How to Run the Project

## Step 1 — Clone the Repository

```bash
git clone https://github.com/yourusername/Customer-Behavior-Analytics.git
```

Move into the project directory:

```bash
cd Customer-Behavior-Analytics
```

---

## Step 2 — Install Required Python Libraries

Install the required libraries:

```bash
pip install pandas sqlalchemy psycopg2-binary
```

Or use the provided requirements file:

```bash
pip install -r requirements.txt
```

---

## Step 3 — Load the Dataset

Make sure the dataset is available inside:

```text
data/customer_shopping_behavior.csv
```

Update the file path in the Jupyter Notebook if required.

---

## Step 4 — Run the Python Notebook

Open:

```text
python/customer_behavior_analysis.ipynb
```

Run the notebook cells in order.

The notebook performs:

```text
Data Loading
      ↓
EDA
      ↓
Missing Value Analysis
      ↓
Data Cleaning
      ↓
Feature Engineering
      ↓
PostgreSQL Data Loading
```

---

## Step 5 — Set Up PostgreSQL

Create a PostgreSQL database named:

```text
customer_behavior
```

Update your local PostgreSQL credentials in the Python notebook.

Example:

```python
username = "postgres"
password = "YOUR_PASSWORD"
host = "localhost"
port = "5432"
database = "customer_behavior"
```

The cleaned DataFrame will be loaded into:

```text
customer
```

---

## Step 6 — Run SQL Queries

Open:

```text
sql/customer_behavior_queries.sql
```

Run the queries in PostgreSQL/pgAdmin to perform the business analysis.

---

## Step 7 — Open Power BI

Open:

```text
powerbi/customer_behavior_dashboard.pbix
```

If required, update the data source connection to your local PostgreSQL/database environment.

The dashboard contains interactive charts, KPIs, and filters for customer behavior analysis.

---

## Step 8 — View the Report

Open:

```text
report/Customer_Behavior_Analytics_Report.pdf
```

The report provides detailed documentation of the project and its findings.

---

## Step 9 — View the Presentation

Open:

```text
presentation/Customer_Behavior_Analytics_Presentation.pdf
```

The presentation summarizes the business problem, methodology, analysis, dashboard, insights, and recommendations.

---

# 📚 Skills Demonstrated

This project demonstrates practical experience with:

* Python
* Pandas
* Exploratory Data Analysis
* Data Cleaning
* Missing Value Treatment
* Feature Engineering
* Data Transformation
* SQL
* PostgreSQL
* SQLAlchemy
* Database Connectivity
* Power BI
* Data Visualization
* Dashboard Development
* Business Analysis
* Business Insights
* Data Storytelling
* Report Writing
* Presentation Development
* Git
* GitHub

---

# 🚀 Key Learning Outcomes

Through this project, I practiced an end-to-end data analytics workflow:

```text
Raw Data
   ↓
Python
   ↓
EDA
   ↓
Data Cleaning
   ↓
Feature Engineering
   ↓
PostgreSQL
   ↓
SQL Analysis
   ↓
Power BI
   ↓
Business Insights
   ↓
Report
   ↓
Presentation
```

The project helped demonstrate how raw customer data can be transformed into structured analysis and interactive business intelligence.

---

# 📌 Conclusion

The **Customer Behavior Analytics** project demonstrates an end-to-end approach to solving a real-world retail analytics problem.

The project combines **Python for data preparation and EDA, PostgreSQL and SQL for structured business analysis, and Power BI for interactive visualization**.

The final outputs include a Power BI dashboard, analytical report, and business presentation, providing a complete view of customer purchasing behavior and category performance.

---

## 👩‍💻 Author

**Sai Suryawanshi**

MCA Graduate | Aspiring Data Analyst

### Skills

**Python | SQL | PostgreSQL | Power BI | Excel | Pandas | Data Analytics | Data Visualization**

---

