# Databricks

> *Unified analytics platform*

---

## What is Databricks?

Databricks is a **unified analytics platform** built on Apache Spark, offering a lakehouse architecture with Delta Lake.

---

## Key Features

| Feature | Description |
|---------|-------------|
| **Lakehouse** | Combines data lake + warehouse |
| **Delta Lake** | ACID transactions on data lake |
| **Unity Catalog** | Unified governance |
| **MLflow** | End-to-end ML lifecycle |
| **Notebooks** | Collaborative development |

---

## Delta Lake

```python
# Write Delta table
df.write.format("delta").save("/path/to/delta-table")

# Read Delta table
df = spark.read.format("delta").load("/path/to/delta-table")

# Time travel
df = spark.read.format("delta").option("versionAsOf", 5).load("/path")

# MERGE (upsert)
deltaTable.alias("target").merge(
    updates.alias("source"),
    "target.id = source.id"
).whenMatchedUpdate(set={"value": "source.value"}) \
 .whenNotMatchedInsert(values={"id": "source.id", "value": "source.value"}) \
 .execute()
```

---

## Medallion Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   BRONZE    │────▶│   SILVER    │────▶│    GOLD     │
│   (Raw)     │     │  (Cleaned)  │     │ (Aggregated)│
└─────────────┘     └─────────────┘     └─────────────┘
```

---

*Back to: [[Index|Tools Home]]*
