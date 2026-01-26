# Prefect

> *Modern workflow orchestration*

---

## What is Prefect?

Prefect is a **modern workflow orchestration** tool that offers a Pythonic API with decorators, making it easier to build and monitor data pipelines.

---

## Prefect vs Airflow

| Aspect | Prefect | Airflow |
|--------|---------|---------|
| **API** | Decorators | DAG files |
| **Dynamic** | Native | TaskFlow (2.0+) |
| **UI** | Modern | Functional |
| **Learning** | Easier | Steeper |
| **Community** | Growing | Massive |

---

## Basic Flow

```python
from prefect import flow, task

@task
def extract():
    return [1, 2, 3]

@task
def transform(data):
    return [x * 2 for x in data]

@task
def load(data):
    print(f"Loaded: {data}")

@flow(name="ETL Pipeline")
def etl_flow():
    raw = extract()
    transformed = transform(raw)
    load(transformed)

if __name__ == "__main__":
    etl_flow()
```

---

## Key Features

- **Decorators**: Simple `@flow` and `@task`
- **Retries**: Built-in retry logic
- **Caching**: Task result caching
- **Scheduling**: Cron and interval schedules
- **UI**: Clean, modern interface

---

*Back to: [[Index|Tools Home]]*
