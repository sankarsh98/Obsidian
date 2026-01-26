# Batch Data Pipeline Project

> *Build an end-to-end batch ETL pipeline*

---

## Project Overview

Build a complete batch data pipeline that:
- **Extracts** data from multiple sources
- **Transforms** using SQL/Python
- **Loads** to a data warehouse
- **Orchestrates** with Airflow

---

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Sources   │     │  Airflow    │     │    dbt      │
│ CSV, API,DB │────▶│ Orchestrate │────▶│  Transform  │
└─────────────┘     └──────┬──────┘     └──────┬──────┘
                           │                    │
                    ┌──────▼──────┐      ┌──────▼──────┐
                    │ S3 / GCS    │      │  Warehouse  │
                    │  (Raw)      │      │ (Snowflake) │
                    └─────────────┘      └─────────────┘
```

---

## Tech Stack

- **Orchestration**: Apache Airflow
- **Storage**: S3 or GCS
- **Warehouse**: Snowflake / BigQuery
- **Transform**: dbt
- **Containers**: Docker

---

## Implementation Steps

### Step 1: Setup Infrastructure
```bash
docker-compose up -d  # Airflow, Postgres
```

### Step 2: Extract Data
```python
@task
def extract_api():
    response = requests.get("https://api.example.com/data")
    return response.json()
```

### Step 3: Load to Storage
```python
@task
def load_to_s3(data):
    s3.put_object(Bucket='raw', Key='data.json', Body=json.dumps(data))
```

### Step 4: Transform with dbt
```sql
-- models/staging/stg_orders.sql
SELECT * FROM {{ source('raw', 'orders') }}
WHERE status != 'cancelled'
```

### Step 5: Schedule DAG
```python
@dag(schedule='@daily')
def batch_pipeline():
    data = extract_api()
    load_to_s3(data)
    run_dbt()
```

---

## Deliverables

- [ ] Working Airflow DAG
- [ ] dbt models with tests
- [ ] Documentation
- [ ] README with setup instructions

---

*Back to: [[Index|Projects Home]]*
