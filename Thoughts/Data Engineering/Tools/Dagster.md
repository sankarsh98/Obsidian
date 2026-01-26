# Dagster

> *Data-aware orchestration*

---

## What is Dagster?

Dagster is a **data-aware orchestrator** that treats data assets as first-class citizens, making it easier to understand data lineage and dependencies.

---

## Key Concepts

### Assets (Software-Defined Assets)

```python
from dagster import asset

@asset
def raw_customers():
    return pd.read_csv("customers.csv")

@asset
def clean_customers(raw_customers):
    return raw_customers.dropna()

@asset
def customer_metrics(clean_customers):
    return clean_customers.groupby('segment').size()
```

### Jobs and Ops

```python
from dagster import job, op

@op
def extract():
    return [1, 2, 3]

@op
def transform(data):
    return [x * 2 for x in data]

@job
def etl_job():
    transform(extract())
```

---

## Dagster vs Airflow

| Aspect | Dagster | Airflow |
|--------|---------|---------|
| **Focus** | Data assets | Tasks |
| **Lineage** | Built-in | Limited |
| **Testing** | Native | External |
| **Dev UX** | Excellent | Good |

---

*Back to: [[Index|Tools Home]]*
