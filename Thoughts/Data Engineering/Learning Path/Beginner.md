# Beginner Learning Track

> *Your first 12 weeks into Data Engineering*

---

## Overview

| Duration | Focus | Outcome |
|----------|-------|---------|
| **Week 1-4** | Foundations | SQL + Python proficiency |
| **Week 5-8** | Core Concepts | Data warehousing understanding |
| **Week 9-12** | First Tools | Airflow + dbt basics |

---

## Week 1-2: SQL Foundations

### Goals
- [ ] Write SELECT queries confidently
- [ ] Master all JOIN types
- [ ] Use aggregations effectively
- [ ] Understand subqueries

### Daily Practice (1-2 hours)

**Day 1-3: Basics**
```sql
-- SELECT, WHERE, ORDER BY
SELECT * FROM customers WHERE country = 'India' ORDER BY created_at DESC;

-- Aggregations
SELECT country, COUNT(*) as customer_count
FROM customers
GROUP BY country
HAVING COUNT(*) > 100;
```

**Day 4-7: JOINs**
```sql
-- Practice all join types
SELECT c.name, o.order_id, o.total
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.order_date >= '2024-01-01';
```

**Day 8-14: Advanced Basics**
```sql
-- Subqueries
SELECT * FROM customers
WHERE id IN (SELECT customer_id FROM orders WHERE total > 1000);

-- CTEs
WITH high_value AS (
    SELECT customer_id, SUM(total) as total_spend
    FROM orders GROUP BY customer_id
    HAVING SUM(total) > 5000
)
SELECT c.*, h.total_spend
FROM customers c JOIN high_value h ON c.id = h.customer_id;
```

### Resources
- [SQLBolt](https://sqlbolt.com/) - Interactive basics
- [Mode SQL Tutorial](https://mode.com/sql-tutorial/)
- [LeetCode Database](https://leetcode.com/problemset/database/) - 50 Easy problems

---

## Week 3-4: Python Foundations

### Goals
- [ ] Core Python syntax mastered
- [ ] File handling (CSV, JSON)
- [ ] pandas basics
- [ ] Simple data transformations

### Daily Practice (1-2 hours)

**Day 1-4: Python Basics**
```python
# Data structures
users = [
    {"id": 1, "name": "Alice", "active": True},
    {"id": 2, "name": "Bob", "active": False}
]

# List comprehension
active_users = [u for u in users if u["active"]]

# Dictionary operations
user_dict = {u["id"]: u["name"] for u in users}
```

**Day 5-7: File Handling**
```python
import csv
import json

# Read CSV
with open('data.csv', 'r') as f:
    reader = csv.DictReader(f)
    data = list(reader)

# Read JSON
with open('data.json', 'r') as f:
    data = json.load(f)

# Write JSON
with open('output.json', 'w') as f:
    json.dump(data, f, indent=2)
```

**Day 8-14: pandas**
```python
import pandas as pd

# Read data
df = pd.read_csv('sales.csv')

# Basic operations
df['revenue'] = df['quantity'] * df['price']
df_grouped = df.groupby('category')['revenue'].sum()

# Filtering
high_revenue = df[df['revenue'] > 1000]

# Save
df.to_parquet('sales.parquet', index=False)
```

### Resources
- [Python for Data Science](https://www.kaggle.com/learn/python)
- [pandas in 10 minutes](https://pandas.pydata.org/docs/user_guide/10min.html)
- [Automate the Boring Stuff](https://automatetheboringstuff.com/)

---

## Week 5-6: Linux & Git

### Goals
- [ ] Navigate command line
- [ ] Basic shell scripting
- [ ] Git workflow mastered
- [ ] Remote work with SSH

### Cheat Sheet

**Essential Linux Commands**
```bash
# Navigation
cd /path/to/dir    # Change directory
ls -la             # List with details
pwd                # Current directory

# File operations
cat file.txt       # View file
head -n 20 file    # First 20 lines
tail -f logfile    # Follow log
grep "pattern" file # Search

# Permissions
chmod 755 script.sh
chown user:group file
```

**Git Workflow**
```bash
# Daily workflow
git pull origin main
git checkout -b feature/my-feature
git add .
git commit -m "feat: add new feature"
git push origin feature/my-feature

# Common operations
git status
git log --oneline -10
git diff
git stash / git stash pop
```

---

## Week 7-8: Data Warehousing Concepts

### Goals
- [ ] Understand OLTP vs OLAP
- [ ] Design a star schema
- [ ] Know SCD types
- [ ] Understand ETL vs ELT

### Key Concepts

**OLTP vs OLAP**
| Aspect | OLTP | OLAP |
|--------|------|------|
| Purpose | Transactions | Analytics |
| Queries | Simple, frequent | Complex, less frequent |
| Data | Current | Historical |
| Design | Normalized | Denormalized |

**Star Schema Example**
```
                    ┌─────────────┐
                    │ dim_date    │
                    └──────┬──────┘
                           │
┌─────────────┐     ┌──────┴──────┐     ┌─────────────┐
│dim_customer │─────│ fact_sales  │─────│ dim_product │
└─────────────┘     └──────┬──────┘     └─────────────┘
                           │
                    ┌──────┴──────┐
                    │ dim_store   │
                    └─────────────┘
```

### Mini-Project: Design a Star Schema
Design tables for an e-commerce system:
- [ ] fact_orders (measures: quantity, revenue, discount)
- [ ] dim_customers (customer details)
- [ ] dim_products (product details)
- [ ] dim_dates (date attributes)

---

## Week 9-10: Docker & Cloud Basics

### Goals
- [ ] Run containers locally
- [ ] Use Docker Compose
- [ ] One cloud platform basics
- [ ] Object storage understanding

### Docker Essentials
```dockerfile
# Dockerfile example
FROM python:3.9-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "main.py"]
```

```yaml
# docker-compose.yml
version: '3'
services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: secret
    ports:
      - "5432:5432"
```

### Cloud First Steps
Choose ONE and complete:
- [ ] Create account with free tier
- [ ] Set up object storage (S3/GCS/Blob)
- [ ] Upload/download files via CLI
- [ ] Understand IAM basics

---

## Week 11-12: First Pipeline

### Goals
- [ ] Install Airflow locally
- [ ] Create first DAG
- [ ] Extract → Transform → Load
- [ ] Schedule and monitor

### Mini-Project: Weather ETL Pipeline

Build a pipeline that:
1. **Extracts** weather data from API daily
2. **Transforms** to structured format
3. **Loads** to PostgreSQL
4. **Schedules** with Airflow

```python
# Simple DAG structure
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def extract():
    # API call to weather service
    pass

def transform():
    # Clean and structure data
    pass

def load():
    # Insert to PostgreSQL
    pass

with DAG('weather_etl', start_date=datetime(2024,1,1), schedule='@daily'):
    t1 = PythonOperator(task_id='extract', python_callable=extract)
    t2 = PythonOperator(task_id='transform', python_callable=transform)
    t3 = PythonOperator(task_id='load', python_callable=load)
    
    t1 >> t2 >> t3
```

---

## Completion Checklist

By end of Week 12, you should be able to:

- [ ] Write complex SQL queries including window functions
- [ ] Process data with pandas and handle files
- [ ] Navigate Linux and use Git confidently
- [ ] Explain star schema and dimensional modeling
- [ ] Run Docker containers and Compose
- [ ] Build and schedule a basic Airflow pipeline
- [ ] Use one cloud platform's basic services

---

## What's Next?

Congratulations! 🎉 You've completed the Beginner Track.

*Continue to: [[Intermediate|Intermediate Track →]]*

*Back to: [[../Index|Data Engineering Home]]*
