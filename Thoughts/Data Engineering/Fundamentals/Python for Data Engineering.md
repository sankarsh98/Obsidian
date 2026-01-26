# Python for Data Engineering

> *Your primary programming tool for building pipelines*

---

## Why Python for DE?

- **pandas** for data manipulation
- **APIs** with requests library
- **File handling** for all formats
- **Integration** with Spark, Airflow, dbt
- **Automation** of repetitive tasks

---

## Core Skills

### 1. Data Structures

```python
# Lists
orders = [100, 200, 150, 300]
total = sum(orders)
filtered = [o for o in orders if o > 150]

# Dictionaries
customer = {
    "id": 1,
    "name": "Alice",
    "orders": [101, 102]
}

# List of dicts (common pattern)
data = [
    {"id": 1, "value": 100},
    {"id": 2, "value": 200}
]
```

### 2. File Handling

```python
import json
import csv

# CSV Reading
with open('data.csv', 'r') as f:
    reader = csv.DictReader(f)
    data = list(reader)

# JSON Reading/Writing
with open('data.json', 'r') as f:
    data = json.load(f)

with open('output.json', 'w') as f:
    json.dump(data, f, indent=2)

# Parquet (preferred for DE)
import pandas as pd
df = pd.read_parquet('data.parquet')
df.to_parquet('output.parquet', index=False)
```

### 3. pandas Essentials

```python
import pandas as pd

# Reading data
df = pd.read_csv('sales.csv')
df = pd.read_parquet('sales.parquet')
df = pd.read_json('sales.json', lines=True)

# Basic operations
df.head()
df.info()
df.describe()
df.columns
df.shape

# Filtering
active = df[df['status'] == 'active']
high_value = df[df['amount'] > 1000]
multi = df[(df['status'] == 'active') & (df['amount'] > 100)]

# New columns
df['revenue'] = df['quantity'] * df['price']
df['year'] = pd.to_datetime(df['date']).dt.year

# Aggregations
summary = df.groupby('category').agg({
    'revenue': 'sum',
    'quantity': 'mean',
    'order_id': 'count'
}).reset_index()

# Joins
merged = pd.merge(orders, customers, on='customer_id', how='left')
```

### 4. API Integration

```python
import requests

# GET request
response = requests.get('https://api.example.com/data')
data = response.json()

# With parameters
params = {'page': 1, 'limit': 100}
response = requests.get('https://api.example.com/data', params=params)

# With headers (authentication)
headers = {'Authorization': 'Bearer YOUR_TOKEN'}
response = requests.get('https://api.example.com/data', headers=headers)

# POST request
payload = {'name': 'test', 'value': 123}
response = requests.post('https://api.example.com/data', json=payload)

# Error handling
response.raise_for_status()  # Raises exception for 4xx/5xx
```

### 5. Database Connections

```python
import psycopg2
from sqlalchemy import create_engine

# psycopg2 (PostgreSQL)
conn = psycopg2.connect(
    host="localhost",
    database="mydb",
    user="user",
    password="pass"
)
cur = conn.cursor()
cur.execute("SELECT * FROM users")
rows = cur.fetchall()

# SQLAlchemy (recommended)
engine = create_engine('postgresql://user:pass@localhost/mydb')

# Read with pandas
df = pd.read_sql("SELECT * FROM users", engine)

# Write with pandas
df.to_sql('users', engine, if_exists='append', index=False)
```

### 6. Chunked Processing (Large Files)

```python
# Process in chunks to manage memory
chunk_size = 100_000
chunks = []

for chunk in pd.read_csv('large_file.csv', chunksize=chunk_size):
    processed = chunk[chunk['status'] == 'active']
    chunks.append(processed)

result = pd.concat(chunks, ignore_index=True)
```

### 7. Error Handling & Logging

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def process_data(file_path):
    try:
        logger.info(f"Processing {file_path}")
        df = pd.read_csv(file_path)
        # Process...
        logger.info(f"Processed {len(df)} rows")
        return df
    except FileNotFoundError:
        logger.error(f"File not found: {file_path}")
        raise
    except Exception as e:
        logger.exception(f"Error processing {file_path}")
        raise
```

---

## Common Patterns

### ETL Function Template

```python
def extract(source: str) -> pd.DataFrame:
    """Extract data from source."""
    return pd.read_parquet(source)

def transform(df: pd.DataFrame) -> pd.DataFrame:
    """Apply business logic."""
    df = df[df['status'] == 'active']
    df['processed_at'] = pd.Timestamp.now()
    return df

def load(df: pd.DataFrame, target: str) -> None:
    """Load data to target."""
    df.to_parquet(target, index=False)

# Run pipeline
if __name__ == "__main__":
    data = extract('input.parquet')
    transformed = transform(data)
    load(transformed, 'output.parquet')
```

---

## Practice Exercises

1. Read a CSV and filter rows based on conditions
2. Merge two DataFrames and calculate aggregates
3. Fetch data from a public API and save as Parquet
4. Process a large file in chunks
5. Build a simple ETL script with logging

---

*Next: [[Data Modeling|Data Modeling →]]*

*Back to: [[Index|Fundamentals Home]]*
