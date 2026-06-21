
# Apache Spark Basics - Week 5 Assignment

---

## Objective

Learn the fundamentals of Apache Spark and use PySpark DataFrames to clean, transform and analyze the Superstore retail dataset — covering the full Extract, Transform, Load (ETL) cycle using distributed in-memory processing.

---
# Apache Spark - Big Data Processing & Data Cleaning

## What is Apache Spark?

Apache Spark is an open-source distributed data processing framework designed for large-scale analytics, machine learning, streaming, and ETL workloads.

### Why Spark?

* In-memory processing
* Faster than MapReduce
* Supports SQL, ML, Streaming & Graph Processing
* Scalable across clusters
* Fault-tolerant architecture

---

## Spark vs MapReduce

| Feature             | MapReduce   | Spark       |
| ------------------- | ----------- | ----------- |
| Processing          | Disk-based  | In-Memory   |
| Speed               | Slower      | Much Faster |
| Iterative ML        | Inefficient | Optimized   |
| Real-Time Analytics | Limited     | Supported   |
| Development         | Complex     | Easier APIs |

### Key Limitations of MapReduce

* Excessive disk I/O
* Multiple read/write operations
* Slow iterative computations
* Complex workflow management

---
### Benefits

* Faster machine learning iterations
* Reduced disk access
* Better performance for analytics
* Efficient caching and reuse


---
## Understanding Shuffle

Shuffle occurs when Spark redistributes data across partitions.

### Common Operations Causing Shuffle

* groupBy()
* join()
* distinct()
* repartition()

## Handling Nulls Before Aggregations

Always clean null values before:

* sum()
* avg()
* min()
* max()

### Benefits

* Prevent inaccurate calculations
* Improve data quality
* Avoid unexpected results

---

## Schema Inference Risk

Using:

```python
inferSchema=True
```
can be risky when data contains inconsistent date formats.

### Potential Issues

* Incorrect data types
* Parsing failures
* Null values introduced
* Data quality problems

### Best Practice

Define schemas explicitly for production workloads.

## Spark Ecosystem

* Spark SQL
* Spark Streaming
* MLlib
* GraphX
* Delta Lake

### Use Cases

* ETL Pipelines
* Data Engineering
* Machine Learning
* Real-Time Analytics
* Data Warehousing

⭐ Spark enables high-performance, scalable, and fault-tolerant big data processing for modern data platforms.


This assignment uses Spark's **DataFrame API**, which provides SQL-like operations on distributed data while Spark handles parallelization under the hood.

---

## Dataset

**Superstore.csv** — Retail sales dataset containing order, customer, product, and sales information across multiple US regions.

Key columns: `Order Date`, `Ship Date`, `Region`, `Category`, `Product Name`, `Sales` (renamed to `Price`), `Quantity`, `Discount`, `Profit`

---

## Pipeline Steps

__Step 1 — Spark Session Setup__
Created a Spark session (`CustomerDataProcessing`) — the entry point for all DataFrame operations.

__Step 2 — Load Data__
Loaded the CSV with explicit handling for embedded commas in text fields:
```python
df = spark.read.csv(file_path, header=True, inferSchema=True,
                     quote='"', escape='"', multiLine=True)
```
> **Key learning:** Product names in the dataset contained commas inside quoted strings (e.g. `"Executive Red, Leather Chair"`). Without `quote` and `escape` options, Spark misaligned columns during parsing, causing numeric fields to receive text values. This is a common real-world data quality issue when ingesting raw CSVs.

Explored the data using `.show()`, `.columns`, `.printSchema()`, and `.count()` to understand structure before transforming.

__Step 3 — Transform Data__

- Renamed `Sales` → `Price` for clarity
- Cast `Price`, `Quantity`, `Discount`, `Profit` to `double` type
- Converted `Ship Date` and `Order Date` strings to proper `date` type using format `M/d/yyyy`
- Verified the new schema with `printSchema()`

__Step 4 — Clean Data__
- Removed duplicate rows with `dropDuplicates()`
- Handled nulls using `na.drop()` and `na.fill()`
- Filtered out rows with invalid/missing `Ship Date`

__Step 5 — Filter & Analyze__
- Filtered records where `Category == "Furniture"`
- Calculated average `Price` across the store using `avg()`
- Counted total items sold per `Region` using `groupBy().count()`
- Calculated total sales per `Region` + `Category` combination using `groupBy().sum()`

__Step 6 — Load (Save Output)__
Wrote the cleaned, transformed DataFrame to disk:
```python
#df.write.mode("overwrite").csv("output")
```

---

## Key Observations

- Raw CSV data required explicit quote/escape handling — `inferSchema` alone is not enough for messy real-world text fields
- Spark's **lazy evaluation** means errors in casting only surface when an action (`.show()`, `.count()`) is triggered, not at the transformation step itself
- `groupBy()` + aggregate functions (`count()`, `sum()`, `avg()`) provide fast distributed aggregation without manual looping
- In-memory processing made repeated transformations on the dataset noticeably fast compared to a traditional disk-based approach

---

## Tech Stack

| Tool | Purpose |
|---|---|
| Apache Spark (PySpark) | Distributed data processing |
| Spark SQL functions | Column transformations and aggregations |
| Jupyter Notebook | Development environment |
| Python 3.13 | Core language |

---

## Repository Structure

```
spark-assignment/
├── data/
│   └── Superstore.csv
├── notebook/
│   └── Spark_Assignment5.ipynb
├── output/
│   └── (cleaned CSV output written by Spark)
└── README.md
```
---

## How to Run

```bash
pip install pyspark
jupyter notebook Spark_Assignment5.ipynb
# Kernel -> Restart & Run All
```
---

## Author

**Heeral Jain**

---
*Celebal Summer Internship 2026 — AnaVerse Data Engineering Track | Week 5*