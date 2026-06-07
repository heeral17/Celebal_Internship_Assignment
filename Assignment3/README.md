Week 3 Assignment

Objective
Build a complete end-to-end data pipeline for a Superstore dataset:

Load raw CSVs into a staging table (superstore_raw)
Design and populate 3 normalized tables using SELECT DISTINCT
Run analytical SQL queries using Subqueries, CTEs, and Window Functions


Database Schema
customers ──(1:N)──> orders
products  ──────────────── (referenced via superstore_raw)

Key Design Decisions

PRAGMA foreign_keys = ON — referential integrity enforced
CREATE TABLE IF NOT EXISTS — idempotent, safe for re-runs
INSERT OR IGNORE + SELECT DISTINCT — duplicate-safe population
CHECK constraints on unit_price > 0 and stock_qty >= 0
UNIQUE constraint on email — no duplicate accounts
5 indexes on high-filter columns for query performance


Repository Structure
Week3_Assignment/
├── Assignment_3.ipynb     <- Complete solution notebook
├── superstore.db          <- SQLite database
├── customer.csv           <- Source data
├── order.csv              <- Source data
├── product.csv            <- Source data
└── README.md              <- This file

 Steps-

1) Connected to superstore.db with FK support
2) Loaded 3 CSVs, merged into superstore_raw staging table
3) Created normalized tables with constraints
4) Created 5 indexes for performance
5) Populated tables using SELECT DISTINCT + INSERT OR IGNORE
6) Verified row counts across all tables
7) Ran 13 analytical SQL queries

Queries Covered
Subqueries

Orders where sales > average sales
Highest sales order per customer (correlated subquery)

CTEs

Total sales per customer
Customers whose total sales exceed average (CTE + Subquery combined)

Window Functions

Rank all customers by total sales — DENSE_RANK()
Assign row numbers per order within each customer — ROW_NUMBER() OVER (PARTITION BY)
Top 3 customers by total sales

Final Combined Query

Customer Name + Total Sales + Rank using JOIN + CTE + Window Function together

Mini Project

Top 5 customers by sales
Bottom 5 customers by sales
Customers who made only one order
Customers with above-average sales
Highest order value per customer


Engineering Practices Applied
python# Reusable functions with docstrings
def create_tables(cursor: sqlite3.Cursor) -> None:
    """Create customers, products, orders with constraints."""

# Safe re-runs
CREATE TABLE IF NOT EXISTS ...
INSERT OR IGNORE INTO ...

# Parameterized queries via SELECT DISTINCT
INSERT OR IGNORE INTO customers
    SELECT DISTINCT customer_id, first_name, ...
    FROM superstore_raw

Every step in a named function with type hints and docstring
Single database superstore.db — no mixed connections
pd.read_sql() for all query outputs — clean readable tables
Verification report after data load


Tech Stack
Python 3.13 Core language 
SQLite3Embedded relational database
PandasCSV loading + query display
Jupyter Notebook  Development environment

How to Run
bash# Clone repo
git clone https://github.com/heeral17/Celebal_Internship_Assignment.git

# Place CSV files in same folder as notebook
# Open notebook
jupyter notebook Assignment_3.ipynb

# Kernel -> Restart & Run All

Author
Heeral Jain


