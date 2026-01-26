# Data Warehouse Project

> *Build a modern cloud data warehouse*

---

## Project Overview

Design and build a data warehouse that:
- Uses **dimensional modeling**
- Implements **dbt transformations**
- Serves **analytics dashboards**

---

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Sources   │     │  Staging    │     │    Marts    │
│ (Raw Data)  │────▶│  (Cleaned)  │────▶│ (Analytics) │
└─────────────┘     └─────────────┘     └─────────────┘
                           │                    │
                    ┌──────┴──────┐      ┌──────▼──────┐
                    │ Snowflake / │      │   Looker /  │
                    │  BigQuery   │      │   Metabase  │
                    └─────────────┘      └─────────────┘
```

---

## Data Model

### Fact Table
```sql
CREATE TABLE fact_orders (
    order_id INT,
    customer_key INT,
    product_key INT,
    date_key INT,
    quantity INT,
    revenue DECIMAL(10,2)
);
```

### Dimension Tables
```sql
CREATE TABLE dim_customers (
    customer_key INT PRIMARY KEY,
    customer_id VARCHAR,
    name VARCHAR,
    segment VARCHAR
);
```

---

## dbt Project Structure

```
dbt_project/
├── models/
│   ├── staging/
│   │   ├── stg_orders.sql
│   │   └── stg_customers.sql
│   └── marts/
│       ├── dim_customers.sql
│       └── fact_orders.sql
└── tests/
```

---

*Back to: [[Index|Projects Home]]*
