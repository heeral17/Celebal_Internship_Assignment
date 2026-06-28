# Spark Architecture & Optimized Data Processing — Week 6 Assignment

## Objective
Understand Spark architecture and perform efficient data processing using transformations, filtering, schema handling, and optimized file formats.

## Spark
Apache Spark or Spark is an open-source distributed data processing framework designed for fast and large-scale data analytics. 
It processes massive datasets by distributing tasks across multiple machines (or cores) in parallel, making it significantly faster than traditional batch-processing frameworks for many workloads.
Spark supports multiple programming languages, including Python (PySpark), Scala, Java, and R, and provides built-in libraries for SQL processing, machine learning, graph processing, and stream processing.

![Architecture](https://raw.githubusercontent.com/heeral17/Celebal_Internship_Assignment/refs/heads/main/Assignment6/photos/Architect.png)


## Key Concepts

### Lazy Evaluation + DAG
Spark does NOT execute transformations immediately. It builds a **DAG (Directed Acyclic Graph)** of all operations and only executes when an **action** is called.

```python
df.filter(...).withColumn(...).groupBy(...)  # Nothing runs yet — just builds DAG
df.show()   # THIS triggers execution of entire DAG
df.count()  # THIS triggers execution
```

**Why it matters:** Spark optimizes the entire pipeline before running — combining steps, pushing filters down, skipping unnecessary reads.

### Transformations vs Actions
| Type | Examples | Executes? |
|---|---|---|
| Narrow Transformation | `filter()`, `select()`, `withColumn()` | No — lazy |
| Wide Transformation | `groupBy()`, `join()`, `distinct()` | No — lazy, causes shuffle |
| Action | `show()`, `count()`, `collect()`, `write()` | Yes — triggers DAG |

### Predicate Pushdown
When reading Parquet, Spark pushes `filter()` conditions into the file reader itself — only matching row groups are loaded into memory, skipping the rest entirely.

```python
# Spark reads ONLY Furniture rows from disk — not the entire file
selected_column.filter(selected_column.Total_amount > 50000)
```

### Wide Transformations & Shuffle
`groupBy()` and `join()` require data to move across partitions — this is called a **shuffle**. Shuffle is expensive because it involves network I/O between executors. Minimize shuffles for better performance.

## CSV vs Parquet

| Feature | CSV | Parquet |
|---|---|---|
| Storage format | Row-based | Columnar |
| Query performance | Reads entire row | Reads only needed columns |
| Schema | No schema enforced | Schema embedded |
| Compression | Poor | Excellent |
| Best for | Small files, human readable | Large scale analytics |

```python
# Reading only Price and Category from 1TB file
# CSV: reads all 50 columns, filters after
# Parquet: reads ONLY Price and Category columns — skips rest
df = spark.read.parquet("data/").select("Price", "Category")
```
## Use Cases:
ETL (Extract, Transform, Load) pipelines
Big data analytics
Real-time data processing
Machine learning
Data warehousing and reporting

### Pipeline Steps
| Step | Operation | Method |
|---|---|---|
| 1 | Load CSV | `spark.read.csv` with quote/escape handling |
| 2 | Convert to Parquet | `df.write.parquet` — columnar, faster reads |
| 3 | Rename + Cast | `withColumnRenamed`, `cast('double')`, `to_date` |
| 4 | Filter (AND) | `(col('Total_amount') > 50000)` |
| 5 | Derive column | `withColumn('total_amount', Price * quantity)` |
| 6 | Aggregate | `groupBy('Category').agg(count)` |
| 7 | Save output | Parquet  using `write.mode('overwrite')` |



## Best Practices Applied

- Used `.show(5)` instead of `.collect()` — avoids driver OOM on large datasets
- Converted CSV to Parquet early — all downstream reads benefit from columnar format
- Used `quote='"'` and `escape='"'` — handles product names with embedded commas
- Used `write.mode("overwrite")` — idempotent, safe for re-runs
- Avoided wide transformations until necessary — narrow ops first to reduce data early

## Repository Structure

```
spark-assignment/
├── Data/
│   ├── Superstore.csv
│   └── Superstore.parquet
├── output/
│   └── (Parquet + CSV output files)
├── Assignment_6.ipynb
└── README.md
|__photos
      |_ Architect.png
      
```

## Tech Stack

| Tool | Purpose |
|---|---|
| Apache Spark 3.x (PySpark) | Distributed data processing |
| Spark SQL functions | Column transformations |
| Parquet | Optimized columnar storage |
| Jupyter Notebook | Development environment |
| Python 3.13 | Core language |


## Author

__Heeral Jain__
Data Engineering Intern — Celebal Technologies (Summer 2026)
