# Delta Lake

> *Open-source storage layer for data lakes*

---

## What is Delta Lake?

Delta Lake brings **ACID transactions** to data lakes, enabling reliable data pipelines on cloud storage.

---

## Key Features

| Feature | Description |
|---------|-------------|
| **ACID Transactions** | Reliable writes |
| **Schema Evolution** | Add columns safely |
| **Time Travel** | Query historical versions |
| **Merge (Upsert)** | Update and insert |
| **Z-Ordering** | Optimize query performance |

---

## Basic Operations

```python
# Write
df.write.format("delta").save("/delta/events")

# Read
df = spark.read.format("delta").load("/delta/events")

# Append
df.write.format("delta").mode("append").save("/delta/events")

# Overwrite
df.write.format("delta").mode("overwrite").save("/delta/events")
```

---

## Time Travel

```python
# By version
df = spark.read.format("delta").option("versionAsOf", 0).load("/delta/events")

# By timestamp
df = spark.read.format("delta").option("timestampAsOf", "2024-01-01").load("/delta/events")

# Show history
from delta.tables import DeltaTable
dt = DeltaTable.forPath(spark, "/delta/events")
dt.history().show()
```

---

## Merge (Upsert)

```python
from delta.tables import DeltaTable

deltaTable = DeltaTable.forPath(spark, "/delta/events")

deltaTable.alias("target").merge(
    updates.alias("source"),
    "target.id = source.id"
).whenMatchedUpdateAll() \
 .whenNotMatchedInsertAll() \
 .execute()
```

---

*Back to: [[Index|Tools Home]]*
