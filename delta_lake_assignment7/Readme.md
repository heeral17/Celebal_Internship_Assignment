# Delta Lake  — Week 7 Assignment


##   Objective
    
Perform incremental data processing using Delta Lake.

![Delta lake](https://github.com/heeral17/Celebal_Internship_Assignment/blob/main/delta_lake_assignment7/photos/Deltalake.png)


## Delta Lake — Why It Matters

| Feature | Delta Lake | Plain Parquet |
|---|---|---|
| ACID transactions | Yes | No |
| MERGE / Upsert | Yes | No |
| Time Travel | Yes | No |
| Schema enforcement | Yes | No |
| Incremental processing | Yes | Full rewrite only |


## Pipeline Architecture

```
CSV (Blob Storage)
      |
      v
[load_csv_to_delta()]      — Ingest + rename columns + write Delta
      |
      v
[clean_dataframe()]        — Drop duplicates, handle nulls
      |
      v
[create_incremental_data()] —  (CDC)
      |
      v
[merge_incremental_data()] — DeltaTable.merge() UPSERT
      |
      v
[validate_delta_table()]   — Row count, duplicate check, spot verify
      |
      v
[show_delta_history()]     — Time travel + transaction log
      |
      v
[display_final_summary()]  — Region + Category aggregations
```


## Pipeline Steps

| Step | Function | What it does |
|---|---|---|
| 1 | `load_csv_to_delta()` | Reads CSV, renames columns (spaces→underscores), writes Delta |
| 2 | `clean_dataframe()` | dropDuplicates, filter nulls on Row_ID, fillna for numeric + string cols |
| 3 | `create_incremental_data()` | Creates 4 records — 2 updates (Row_ID 1,2) + 2 inserts (Row_ID 9995,9996) |
| 4 | `merge_incremental_data()` | MERGE on Row_ID — UPDATE matched, INSERT unmatched |
| 5 | `validate_delta_table()` | Checks row count, zero duplicates, zero nulls, spot verifies merged rows |
| 6 | `show_delta_history()` | Displays Delta transaction log with version + operation |
| 7 | `display_final_summary()` | Region-wise sales + category-wise order count |



## MERGE Operation — Core of This Assignment

```python
delta_table.alias("target")
.merge(
    df_incremental.alias("source"),
    "target.Row_ID = source.Row_ID"     # merge key
)
.whenMatchedUpdateAll()                 # UPDATE if Row_ID exists
.whenNotMatchedInsertAll()              # INSERT if Row_ID is new
.execute()
```

**Why MERGE over overwrite:**
- Full overwrite rewrites entire table — expensive at scale
- MERGE only touches affected rows — efficient for daily/hourly incremental loads
- ACID guarantees — partial failure = automatic rollback


## SCD Type 1 vs Type 2

| Type | Strategy | History kept? | This assignment |
|---|---|---|---|
| SCD Type 1 | Overwrite on match | No | Yes — implemented |
| SCD Type 2 | Add new row with `is_current` + `valid_to` | Yes | Not implemented |

## Delta Time Travel

```python
# Read version 0 — original load before any MERGE
spark.read.format("delta").option("versionAsOf", 0).load(DELTA_PATH)

# Read version 1 — after MERGE
spark.read.format("delta").option("versionAsOf", 1).load(DELTA_PATH)
```

Delta stores every transaction in `_delta_log/` as JSON — enabling full audit trail and point-in-time recovery.

## Validation Report (Expected Output)

```
About updated & inserted rows
```

## Engineering Practices Applied

- Every step in a named function with docstring and type hints
- `DELTA_PATH` and `CSV_PATH` defined  at top — easy to change
- `whenMatchedUpdateAll()` + `whenNotMatchedInsertAll()` — Delta MERGE
- Post-merge validation checks — not just assuming it worked
- `show()` used throughout — never `collect()` on large data

## Repository Structure

```
delta-lake-assignment/
│
├── data/
│   ├── customer_master.csv
│   └── customer_incremental.csv
│
├── notebooks/
│   └── delta_scd_assignment.ipynb
│
├── screenshots/
│   ├── data_loading/
│   ├── data_cleaning/
│   ├── scd1/
│   ├── scd2/
│   ├── validation/
│   └── final_output/
│
├── report/
│   └── assignment_summary.pdf (optional)
│
└── README.md
```

## Tech Stack

| Tool | Purpose |
|---|---|
| Databricks | Cloud notebook + cluster environment |
| Delta Lake | ACID storage format with versioning |
| PySpark | Distributed data processing |
| Azure ADLS / Volumes | Data storage |



## Author

__Heeral Jain__
