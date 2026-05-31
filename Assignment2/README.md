Overview-
Designed and queried a normalized relational database for ShopEase, a mid-sized e-commerce company selling multiple products across India. The goal was to extract meaningful business insights using structured SQL queries across 5 progressive sections — from basic retrieval to advanced transaction management.

Database Schema
customers ──(1:N)──▶ orders ──(1:N)──▶ order_items ◀──(N:1)── products


Key Design Decisions

Primary Keys on all tables for unique row identification
Foreign Keys enforcing referential integrity across all relationships
CHECK constraints preventing invalid data (unit_price > 0, stock_qty >= 0, discount_pct BETWEEN 0 AND 100)
UNIQUE constraint on customers.email preventing duplicate accounts
5 indexes created on high-filter columns (city, state, category, customer_id, order_id) for query optimization


 Repository Structure
 Week2_SQL_Assignment/
├── 📓 Assignment_2.ipynb     ← Complete solution with outputs
├── 🗄️  Sales_analysis.db     ← SQLite database (all tables + data)
└── 📄 README.md              ← This file

🔍 Sections Covered
Section A — SQL Basics

Full table retrieval and selective column queries
Primary Key identification and justification across all 4 tables
UNIQUE + NOT NULL constraint behavior on email column
CHECK constraint violation demonstration with error handling

Section B — Filtering & Optimization

Status-based, category-based, and date-range filtering
Multi-condition WHERE clauses combining AND, BETWEEN, <>
Index behavior explanation — idx_orders_date performance impact
SARGable query rewriting — why YEAR(join_date) breaks index usage and how to fix it

Section C — Aggregation

COUNT, SUM, AVG, MIN, MAX across orders and products
GROUP BY with category-wise price analysis
HAVING clause filtering categories by average price threshold

Section D — Joins & Relationships

INNER JOIN across customers ↔ orders
LEFT JOIN for complete customer listing including those with no orders
3-table JOIN chain: orders → order_items → products
LEFT vs RIGHT JOIN conceptual comparison
Foreign Key violation behavior explained with real example

Section E — Advanced SQL

CASE-based product price tier classification (Budget / Mid-Range / Premium)
CASE inside aggregate — delivered vs non-delivered order count in single row
ACID properties explained with real-world bank transfer analogy
Full BEGIN...COMMIT/ROLLBACK transaction block with stock update and error handling


Tech Stack
Python (Programming language)
Sqlite 3(Relational Database)
Jupyter Notebook

Engineering Practices Applied
python# Every step wrapped in reusable functions with docstrings
def create_tables(cursor: sqlite3.Cursor) -> None:
    """Create all tables with IF NOT EXISTS to avoid duplicate errors."""

# Parameterized queries — 
cursor.executemany("INSERT OR IGNORE INTO customers VALUES (?,?,?,?,?,?,?,?)", customers)

# Transaction with full ROLLBACK safety
try:
    cursor.execute("BEGIN")
    # ... operations ...
    conn.commit()
except Exception as e:
    conn.rollback()
    print(f"Transaction rolled back: {e}")

CREATE TABLE IF NOT EXISTS — idempotent, safe for re-runs
INSERT OR IGNORE — duplicate-safe data loading
executemany() with ? placeholders — parameterized, injection-safe
PRAGMA foreign_keys = ON — enforcing FK constraints in SQLite
Type hints on all functions — production code standard


 How to Run
bash# Clone the repository
git clone https://github.com/heeral17/Internship_Assignment.git

# Open the notebook
jupyter notebook Assignment_2.ipynb

# Run All Cells (Kernel → Restart & Run All)

Note: The SQLite database Sales_analysis.db is included — no external database setup required.
