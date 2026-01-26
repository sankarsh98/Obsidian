# Apache Airflow

> *The platform for programmatically authoring, scheduling, and monitoring workflows*

---

## What is Airflow?

Airflow is a **workflow orchestration tool** that lets you define, schedule, and monitor data pipelines as code.

---

## Core Concepts

### DAG (Directed Acyclic Graph)

A DAG defines the workflow - what tasks to run and in what order.

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime, timedelta

default_args = {
    'owner': 'data-team',
    'retries': 3,
    'retry_delay': timedelta(minutes=5)
}

with DAG(
    dag_id='my_first_dag',
    default_args=default_args,
    description='My first DAG',
    start_date=datetime(2024, 1, 1),
    schedule='@daily',  # or cron: '0 6 * * *'
    catchup=False
) as dag:
    pass
```

### Operators

| Operator | Purpose |
|----------|---------|
| `PythonOperator` | Run Python code |
| `BashOperator` | Run bash commands |
| `PostgresOperator` | Run SQL on Postgres |
| `S3ToRedshiftOperator` | Load S3 to Redshift |
| `DummyOperator` | Placeholder/dependency |

---

## Building DAGs

### Basic Example

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def extract():
    print("Extracting data...")
    return {"data": [1, 2, 3]}

def transform(ti):
    data = ti.xcom_pull(task_ids='extract_task')
    print(f"Transforming: {data}")
    return {"transformed": True}

def load(ti):
    data = ti.xcom_pull(task_ids='transform_task')
    print(f"Loading: {data}")

with DAG('etl_pipeline', start_date=datetime(2024, 1, 1), schedule='@daily') as dag:
    
    extract_task = PythonOperator(
        task_id='extract_task',
        python_callable=extract
    )
    
    transform_task = PythonOperator(
        task_id='transform_task',
        python_callable=transform
    )
    
    load_task = PythonOperator(
        task_id='load_task',
        python_callable=load
    )
    
    # Define dependencies
    extract_task >> transform_task >> load_task
```

### Task Dependencies

```python
# Sequential
task1 >> task2 >> task3

# Parallel then merge
[task1, task2] >> task3

# Complex
task1 >> [task2, task3]
task2 >> task4
task3 >> task4
```

---

## Key Features

### XComs (Cross-Communication)

Pass data between tasks:

```python
def push_data(ti):
    ti.xcom_push(key='my_key', value={'result': 42})

def pull_data(ti):
    value = ti.xcom_pull(task_ids='push_task', key='my_key')
```

### Connections & Variables

```python
from airflow.hooks.base import BaseHook

# Get connection
conn = BaseHook.get_connection('my_postgres')
print(conn.host, conn.login, conn.password)

# Get variable
from airflow.models import Variable
api_key = Variable.get('API_KEY')
```

### TaskFlow API (Airflow 2.0+)

```python
from airflow.decorators import dag, task
from datetime import datetime

@dag(start_date=datetime(2024, 1, 1), schedule='@daily')
def my_dag():
    
    @task
    def extract():
        return {"data": [1, 2, 3]}
    
    @task
    def transform(data):
        return {"transformed": data}
    
    @task
    def load(data):
        print(f"Loading {data}")
    
    # Automatic XCom passing!
    data = extract()
    transformed = transform(data)
    load(transformed)

my_dag()
```

---

## Docker Setup

```yaml
# docker-compose.yml (simplified)
version: '3'
services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_PASSWORD: airflow
      POSTGRES_USER: airflow
      POSTGRES_DB: airflow

  webserver:
    image: apache/airflow:2.7.0
    depends_on:
      - postgres
    environment:
      AIRFLOW__CORE__EXECUTOR: LocalExecutor
      AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
    ports:
      - "8080:8080"
    volumes:
      - ./dags:/opt/airflow/dags
```

---

## Best Practices

1. **Keep DAGs idempotent** - Safe to re-run
2. **Use incremental loads** - Don't reload everything
3. **Avoid heavy logic in DAG file** - Keep it clean
4. **Use pools** - Control concurrency
5. **Set proper timeouts** - Prevent stuck tasks

---

*Next: [[dbt|dbt →]]*

*Back to: [[Index|Tools Home]]*
