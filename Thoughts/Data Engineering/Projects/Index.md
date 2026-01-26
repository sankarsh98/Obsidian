# Projects Index

> *Build your portfolio with these hands-on projects*

---

## Why Projects Matter

| Purpose | Benefit |
|---------|---------|
| **Learning** | Apply concepts in real scenarios |
| **Portfolio** | Showcase skills to employers |
| **Interviews** | Discuss real implementations |
| **Confidence** | Prove you can build end-to-end |

---

## Project Levels

### 🟢 Beginner Projects

| Project | Skills | Time |
|---------|--------|------|
| [[Batch Pipeline\|CSV to Database ETL]] | Python, SQL, pandas | 1 week |
| [[API Data Ingestion]] | APIs, JSON, scheduling | 1 week |
| [[Basic Data Warehouse]] | Dimensional modeling, SQL | 2 weeks |

### 🟡 Intermediate Projects

| Project | Skills | Time |
|---------|--------|------|
| [[Batch Pipeline\|Airflow ELT Pipeline]] | Airflow, dbt, Cloud | 2-3 weeks |
| [[Streaming Pipeline\|Kafka Real-time]] | Kafka, Spark, dashboards | 3-4 weeks |
| [[Data Warehouse\|Cloud Data Warehouse]] | Snowflake/BigQuery, dbt | 3-4 weeks |

### 🔴 Advanced Projects

| Project | Skills | Time |
|---------|--------|------|
| [[Full Data Platform]] | End-to-end, multi-source | 4-6 weeks |
| [[ML Pipeline]] | Feature store, MLOps | 4-6 weeks |
| [[Data Mesh Implementation]] | Architecture, governance | 4-6 weeks |

---

## Featured Project: E-commerce Analytics Platform

### Overview
Build a complete data platform for an e-commerce business.

### Architecture
```
Sources                 Ingestion        Storage          Transform        Serve
────────               ──────────       ─────────        ──────────       ──────
PostgreSQL  ───┐                        
               ├──→  Airbyte/    ──→   Data Lake   ──→    dbt      ──→  Dashboards
REST APIs   ───┤     Airflow           (S3/GCS)          Models         (Metabase)
               │                            │               │
CSV Files   ───┘                            └───────────────┘
                                           Data Warehouse
                                          (Snowflake/BQ)
```

### Components
1. **Data Sources**
   - Operational PostgreSQL (orders, customers)
   - External APIs (payments, shipping)
   - CSV uploads (marketing data)

2. **Ingestion Layer**
   - Airbyte for database CDC
   - Custom Python for APIs
   - Airflow orchestration

3. **Storage**
   - Raw layer (S3/GCS)
   - Warehouse (Snowflake/BigQuery)

4. **Transformation**
   - dbt models (staging, marts)
   - Data quality tests
   - Documentation

5. **Serving**
   - Business dashboards
   - Ad-hoc SQL access
   - API for applications

---

## Project Template

### README Structure
```markdown
# Project Name

## Overview
Brief description of what this project does.

## Architecture
Diagram or description of components.

## Technologies Used
- List of tools and why chosen

## Setup
Step-by-step instructions to run locally.

## Data Flow
1. Extraction step
2. Transformation step
3. Loading step

## Lessons Learned
What you learned building this.

## Future Improvements
What you would add with more time.
```

### Repository Structure
```
project/
├── README.md
├── docker-compose.yml
├── .env.example
├── src/
│   ├── extract/
│   ├── transform/
│   └── load/
├── dbt/
│   ├── models/
│   └── tests/
├── airflow/
│   └── dags/
├── tests/
└── docs/
```

---

## Quick Start Projects

### Project 1: API to Database (Weekend Project)

**Goal:** Extract data from a public API and load to PostgreSQL

```python
# main.py
import requests
import psycopg2
from datetime import datetime

def extract_weather(city):
    url = f"https://api.open-meteo.com/v1/forecast?latitude=52.52&longitude=13.41&current_weather=true"
    return requests.get(url).json()

def load_to_db(data):
    conn = psycopg2.connect("postgresql://user:pass@localhost/db")
    cur = conn.cursor()
    cur.execute("""
        INSERT INTO weather (temp, time, recorded_at)
        VALUES (%s, %s, %s)
    """, (
        data['current_weather']['temperature'],
        data['current_weather']['time'],
        datetime.now()
    ))
    conn.commit()

if __name__ == "__main__":
    data = extract_weather("Berlin")
    load_to_db(data)
```

### Project 2: CSV Aggregation (1 Day)

**Goal:** Read CSVs, aggregate, and output summary

```python
import pandas as pd
from pathlib import Path

def process_sales(input_dir: str, output_file: str):
    # Read all CSVs
    dfs = []
    for file in Path(input_dir).glob("*.csv"):
        dfs.append(pd.read_csv(file))
    
    df = pd.concat(dfs, ignore_index=True)
    
    # Aggregate
    summary = df.groupby(['product_category', 'region']).agg({
        'revenue': 'sum',
        'quantity': 'sum',
        'order_id': 'count'
    }).reset_index()
    
    summary.columns = ['category', 'region', 'total_revenue', 'total_qty', 'order_count']
    summary.to_parquet(output_file, index=False)
    
    return summary

if __name__ == "__main__":
    result = process_sales("data/sales/", "output/sales_summary.parquet")
    print(result)
```

---

## Project Ideas by Domain

| Domain | Project Idea |
|--------|--------------|
| **Finance** | Stock price tracker with alerts |
| **E-commerce** | Order analytics dashboard |
| **Social Media** | Twitter/Reddit sentiment analysis |
| **Sports** | Match statistics aggregator |
| **Weather** | Historical weather data warehouse |
| **Music** | Spotify listening history analysis |
| **News** | Article categorization pipeline |

---

## Showcase Tips

1. **Host your code** on GitHub with clean README
2. **Deploy if possible** (free tiers available)
3. **Write a blog post** explaining your approach
4. **Create architecture diagrams** (draw.io, Excalidraw)
5. **Record a demo video** (Loom, 5 minutes)

---

*Start building: [[Batch Pipeline|First Project →]]*

*Back to: [[../Index|Data Engineering Home]]*
