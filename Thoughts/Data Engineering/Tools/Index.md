# Tools & Technologies Index

> *The Modern Data Stack explained*

---

## The Data Engineering Toolkit

```
┌─────────────────────────────────────────────────────────────────┐
│                      MODERN DATA STACK                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  INGESTION        STORAGE         PROCESSING      SERVING      │
│  ─────────        ───────         ──────────      ───────      │
│  Fivetran         Snowflake       Spark           Tableau      │
│  Airbyte          BigQuery        dbt             Looker       │
│  Kafka            Databricks      Airflow         Metabase     │
│  Debezium         S3/GCS          Flink           Superset     │
│                   Delta Lake                                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔧 Essential Tools

### Processing Engines

| Tool | Type | Learn Priority | Use Case |
|------|------|----------------|----------|
| [[Apache Spark]] | Batch/Streaming | 🔴 Must Learn | Large-scale processing |
| [[Apache Flink]] | Streaming | 🟠 Important | Real-time analytics |
| [[dbt]] | Transform | 🔴 Must Learn | SQL transformations |

---

### Orchestration

| Tool | Type | Learn Priority | Use Case |
|------|------|----------------|----------|
| [[Apache Airflow]] | Workflow | 🔴 Must Learn | Pipeline scheduling |
| [[Prefect]] | Workflow | 🟡 Alternative | Modern orchestration |
| [[Dagster]] | Workflow | 🟡 Alternative | Data-aware orchestration |

---

### Streaming

| Tool | Type | Learn Priority | Use Case |
|------|------|----------------|----------|
| [[Apache Kafka]] | Message Queue | 🔴 Must Learn | Event streaming |
| AWS Kinesis | Message Queue | 🟠 Cloud-specific | AWS streaming |
| Google Pub/Sub | Message Queue | 🟠 Cloud-specific | GCP streaming |

---

### Storage

| Tool | Type | Learn Priority | Use Case |
|------|------|----------------|----------|
| [[Snowflake]] | Cloud DW | 🟠 Important | Cloud data warehouse |
| [[Databricks]] | Lakehouse | 🟠 Important | Unified analytics |
| BigQuery | Cloud DW | 🟠 Important | GCP analytics |
| Redshift | Cloud DW | 🟠 Important | AWS analytics |
| [[Delta Lake]] | Table Format | 🟠 Important | ACID on data lake |

---

### [[Cloud Platforms]]

| Platform | Key Services | Certification |
|----------|--------------|---------------|
| **AWS** | S3, Glue, EMR, Redshift, Kinesis | Data Analytics Specialty |
| **GCP** | GCS, Dataflow, BigQuery, Pub/Sub | Professional Data Engineer |
| **Azure** | ADLS, Synapse, Data Factory | Data Engineer Associate |

---

## Learning Paths by Tool

### Path 1: Batch Processing Focus
```
SQL → Python → Airflow → Spark → dbt → Cloud DW
```

### Path 2: Streaming Focus
```
SQL → Python → Kafka → Spark Streaming → Flink → Cloud
```

### Path 3: Cloud-Native Focus
```
SQL → Python → Cloud Basics → Managed Services → IaC
```

---

## Tool Comparison Matrix

### Orchestration Showdown

| Feature | Airflow | Prefect | Dagster |
|---------|---------|---------|---------|
| Maturity | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| Learning Curve | Steep | Moderate | Moderate |
| UI | Good | Excellent | Excellent |
| Python Native | DAG files | Decorators | Decorators |
| Community | Massive | Growing | Growing |
| Best For | Production | Flexibility | Data Assets |

### Data Warehouse Showdown

| Feature | Snowflake | BigQuery | Redshift | Databricks |
|---------|-----------|----------|----------|------------|
| Pricing Model | Credits | On-demand | Node-based | DBUs |
| Separation | Compute/Storage | Yes | Limited | Yes |
| Semi-structured | Excellent | Good | Good | Excellent |
| ML Integration | Cortex | BQML | SageMaker | MLflow |
| Best For | General | GCP users | AWS users | Unified |

---

## Quick Start Guides

### Airflow in 5 Minutes
```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def my_task():
    print("Hello, Data Engineering!")

with DAG('my_first_dag', start_date=datetime(2024,1,1), schedule='@daily') as dag:
    task = PythonOperator(task_id='hello', python_callable=my_task)
```

### Spark in 5 Minutes
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("MyApp").getOrCreate()
df = spark.read.parquet("s3://bucket/data/")
df.filter(df.status == "active").groupBy("category").count().show()
```

### dbt in 5 Minutes
```sql
-- models/marts/dim_customers.sql
{{
    config(materialized='table')
}}

SELECT
    customer_id,
    first_name,
    last_name,
    created_at,
    DATEDIFF(day, created_at, CURRENT_DATE) as days_since_signup
FROM {{ ref('stg_customers') }}
```

---

## Installation Checklist

### Local Development Setup
- [ ] Python 3.9+ with virtual environments
- [ ] Docker Desktop
- [ ] IDE (VS Code recommended)
- [ ] Git
- [ ] PostgreSQL (local testing)
- [ ] DBeaver or similar SQL client

### Cloud Accounts (Free Tiers)
- [ ] AWS Account
- [ ] GCP Account
- [ ] Snowflake Trial
- [ ] Databricks Community Edition

---

*Deep dive: [[Apache Spark|Start with Spark →]]*

*Back to: [[../Index|Data Engineering Home]]*
