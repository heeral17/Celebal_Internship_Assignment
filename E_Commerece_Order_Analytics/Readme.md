# Project: E-Commerce Order Analytics System

## Objective

You are joining a company that processes online orders. The raw data comes from multiple sources and is often incomplete, inconsistent, or duplicated. The objective of this project is to clean and validate the data, transform it into an analytics-ready format, store it in a relational database, and generate business reports that support decision-making.


## Dataset

customers.csv(customer_id, customer_name, email, registration_date, customer_type)

products.csv(product_id, product_name, category, subcategory, cost_price)

orders.csv(order_id, customer_id, order_date, status, region_code)

order_items.csv(item_id, order_id, product_id, quantity, unit_price, discount_percent)


## Project Workflow

```text
Raw CSV Files
      ↓
Data Profiling
      ↓
Data Cleaning & Validation
      ↓
SQLite Database
      ↓
SQL Analytics
      ↓
Business Reports
```


## Step 1 — Data Generation

Created realistic synthetic e-commerce datasets using the Faker library.
<from faker import Faker>

Generated:

* Customers names
* Products titles
* Orders dates
* Order Items
* Customers emails 

The generated data intentionally contains quality issues such as:

* Missing values
* Duplicate records
* Invalid discounts
* Future dates
* invalid emails
  
These issues simulate real-world operational datasets.


## Step 2 — Data Cleaning & Validation

Performed data quality checks and cleaning operations.

### Data Quality Checks

* Removed duplicate records
* Handled missing values
* Standardized date formats
* Validated foreign key relationships
* Removed orphan order items
* Validated discount percentage ranges
* Checked for zero or negative quantities
* Identified future-dated orders

### Validation Rules

* discount_percent must be between 0 and 100
* quantity must be greater than 0
* order_date cannot be in the future
* order_items.order_id must exist in orders
* order_items.product_id must exist in products

## Step 3 — Database Design & SQL Analytics

Loaded cleaned datasets into SQLite.
<conn = sqlite3.connect(db_name)>


### Schema

* customers
* products
* orders
* order_items

### Analytical Queries Implemented

* Monthly Revenue Analysis
* Year-over-Year Revenue Growth
* Top Products by Revenue
* Customer Lifetime Value (CLV)
* Category Performance Analysis
* Revenue Contribution by Customer Segment
* Product Pair (Frequently Bought Together) Analysis
* Cohort Retention Analysis
* Customer Category Shift Analysis
* Revenue Concentration (Pareto Analysis)

### SQL Concepts Used

* Joins
* Common Table Expressions (CTEs)
* Window Functions
* Ranking Functions
* Conditional Aggregation
* Cohort Analysis
* Self Joins

## Step 4 — Python + SQL Integration

Developed a command-line reporting tool.

### Features

* User selects report type
* User enters date range
* Connects dynamically to SQLite
* Generates business summary

### Report Metrics

* Total Orders
* Revenue
* Unique Customers
* Top 3 Products
* Previous Period Comparison
* Growth Percentage Analysis

## Step 5 — Edge Case Handling

Implemented validation tests for common data quality issues.

### Test Cases

* Orphan order items
* Discounts greater than 100%
* Zero quantity records
* Future-dated orders

### Error Handling

* Input validation
* Database exception handling
* Logging of invalid records
* Graceful failure reporting

## Key Observations

* Duplicate records significantly impact revenue calculations and customer metrics.
* Referential integrity checks are critical when combining multiple source systems.
* Window functions simplify advanced business analytics such as retention and ranking.
* Data validation before loading prevents inaccurate reporting.
* Parameterized queries improve security and maintainability.


## Tech Stack
1)Python Libraries-
   PANDAS
   LOGGING
   FAKER
   RE
   OS
2)SQL


## Repository Structure

```text
E-Commerce Order Analytics System/
├── data/
│   ├── customers.csv
│   ├── products.csv
│   ├── orders.csv
│   └── order_items.csv
│-──cleaned_data/
|   ├── customers.csv
│   ├── products.csv
│   ├── orders.csv
│   └── order_items.csv
├── database/
│   └── ecommerce_analysis.db
│
├── reports/
│   └── generated_reports/
│
├── notebook/
│   └── Ecommerce_Order_Analytics.ipynb
│
└── README.md
```

## How to Run

```bash
pip install pandas faker

jupyter notebook Ecommerce_Order_Analytics.ipynb
```
Run all notebook cells in sequence to:

1. Generate datasets
2. Clean and validate data
3. Load data into SQLite
4. Execute analytical SQL queries
5. Generate business reports
6. Run edge case validation tests

## Author 
Heeral Jain
