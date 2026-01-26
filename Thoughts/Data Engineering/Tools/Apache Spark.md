# Apache Spark

> *The unified analytics engine for large-scale data processing*

---

## What is Spark?

Apache Spark is a **distributed computing framework** for processing large datasets across clusters. It's the most widely used tool for big data processing.

---

## Key Concepts

### Architecture

```
           ┌──────────────┐
           │ Driver       │
           │ (SparkContext)│
           └──────┬───────┘
                  │
    ┌─────────────┼─────────────┐
    ▼             ▼             ▼
┌────────┐   ┌────────┐   ┌────────┐
│Executor│   │Executor│   │Executor│
│(Worker)│   │(Worker)│   │(Worker)│
└────────┘   └────────┘   └────────┘
```

### Core Abstractions

| Abstraction | Description |
|-------------|-------------|
| **RDD** | Resilient Distributed Dataset (low-level) |
| **DataFrame** | Structured data with schema (recommended) |
| **Dataset** | Type-safe DataFrame (Scala/Java) |

---

## PySpark Essentials

### Setup & SparkSession

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("MyApp") \
    .config("spark.executor.memory", "4g") \
    .getOrCreate()
```

### Reading Data

```python
# Parquet (best performance)
df = spark.read.parquet("s3://bucket/data/")

# CSV
df = spark.read.option("header", "true").csv("data.csv")

# JSON
df = spark.read.json("data.json")

# From database
df = spark.read \
    .format("jdbc") \
    .option("url", "jdbc:postgresql://host/db") \
    .option("dbtable", "users") \
    .load()
```

### Transformations

```python
from pyspark.sql import functions as F

# Select columns
df.select("id", "name", "email")

# Filter
df.filter(F.col("status") == "active")
df.filter(df.amount > 1000)

# Add column
df.withColumn("revenue", F.col("quantity") * F.col("price"))

# Rename
df.withColumnRenamed("old_name", "new_name")

# Aggregations
df.groupBy("category").agg(
    F.sum("revenue").alias("total_revenue"),
    F.count("*").alias("count")
)

# Joins
df1.join(df2, df1.id == df2.user_id, "left")

# Window functions
from pyspark.sql.window import Window
window = Window.partitionBy("customer_id").orderBy("date")
df.withColumn("row_num", F.row_number().over(window))
```

### Actions

```python
# Collect to driver (be careful with size!)
results = df.collect()

# Show preview
df.show(10)

# Count rows
df.count()

# Write
df.write.parquet("output/")
df.write.mode("overwrite").parquet("output/")
```

### Spark SQL

```python
# Register as temp view
df.createOrReplaceTempView("orders")

# Run SQL
result = spark.sql("""
    SELECT 
        customer_id,
        SUM(amount) as total
    FROM orders
    GROUP BY customer_id
    HAVING SUM(amount) > 1000
""")
```

---

## Performance Tuning

### Key Configurations

| Config | Purpose |
|--------|---------|
| `spark.executor.memory` | Memory per executor |
| `spark.executor.cores` | Cores per executor |
| `spark.sql.shuffle.partitions` | Partitions for shuffles (default 200) |
| `spark.default.parallelism` | Default parallelism |

### Best Practices

1. **Avoid collect()** on large data
2. **Cache strategically**: `df.cache()` or `df.persist()`
3. **Use broadcast** for small tables
4. **Repartition wisely** - too many or too few partitions hurt
5. **Prefer DataFrames** over RDDs

```python
# Broadcast join for small tables
from pyspark.sql.functions import broadcast
large_df.join(broadcast(small_df), "key")

# Repartition
df.repartition(100, "partition_column")
df.coalesce(10)  # Reduce partitions
```

---

## Common Patterns

### ETL Pipeline

```python
def extract():
    return spark.read.parquet("s3://raw/")

def transform(df):
    return df \
        .filter(F.col("status") == "active") \
        .withColumn("processed_at", F.current_timestamp())

def load(df):
    df.write.mode("overwrite").parquet("s3://processed/")

# Run
raw = extract()
transformed = transform(raw)
load(transformed)
```

---

*Next: [[Apache Kafka|Kafka →]]*

*Back to: [[Index|Tools Home]]*
