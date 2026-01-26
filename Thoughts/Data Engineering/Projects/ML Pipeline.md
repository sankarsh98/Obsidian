# ML Pipeline Project

> *Build data infrastructure for machine learning*

---

## Project Overview

Build the data layer for ML:
- Feature engineering pipelines
- Feature store
- Training data generation
- Model serving data

---

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Sources   │────▶│  Features   │────▶│   Feature   │
│             │     │  Pipeline   │     │    Store    │
└─────────────┘     └─────────────┘     └──────┬──────┘
                                               │
                        ┌──────────────────────┴───────┐
                        ▼                              ▼
                 ┌─────────────┐               ┌─────────────┐
                 │  Training   │               │   Online    │
                 │    Data     │               │   Serving   │
                 └─────────────┘               └─────────────┘
```

---

## Feature Engineering

```python
# Example feature computation
@asset
def customer_features(orders, customers):
    return orders.groupby('customer_id').agg({
        'order_id': 'count',
        'total': ['sum', 'mean'],
        'created_at': 'max'
    }).reset_index()
```

---

## Tools

- **Feature Store**: Feast, Tecton
- **Orchestration**: Airflow, Prefect
- **ML Platform**: MLflow, Kubeflow
- **Serving**: Redis, DynamoDB

---

*Back to: [[Index|Projects Home]]*
