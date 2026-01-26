# Snowflake

> *The Data Cloud platform*

---

## What is Snowflake?

Snowflake is a **cloud-native data warehouse** with unique architecture separating compute and storage.

---

## Key Features

| Feature | Description |
|---------|-------------|
| **Separation** | Compute and storage independent |
| **Scaling** | Scale up/down in seconds |
| **Zero-copy cloning** | Instant data copies |
| **Time Travel** | Query historical data |
| **Data Sharing** | Share data across accounts |

---

## Architecture

```
┌─────────────────────────────────────────┐
│           Cloud Services                │
│    (Query optimization, security)       │
└─────────────────────────────────────────┘
                    │
┌───────────────────┼───────────────────┐
│      Virtual Warehouses (Compute)     │
│  ┌─────────┐  ┌─────────┐  ┌────────┐│
│  │   XS    │  │    S    │  │   M    ││
│  └─────────┘  └─────────┘  └────────┘│
└───────────────────────────────────────┘
                    │
┌───────────────────┼───────────────────┐
│         Cloud Storage (Data)          │
│         (S3, Azure Blob, GCS)         │
└───────────────────────────────────────┘
```

---

## Basic SQL

```sql
-- Create warehouse
CREATE WAREHOUSE my_warehouse
  WAREHOUSE_SIZE = 'SMALL'
  AUTO_SUSPEND = 300
  AUTO_RESUME = TRUE;

-- Create database and schema
CREATE DATABASE my_db;
CREATE SCHEMA my_db.analytics;

-- Create table
CREATE TABLE my_db.analytics.customers (
    customer_id INT,
    name VARCHAR,
    created_at TIMESTAMP
);

-- Time travel (query past data)
SELECT * FROM customers AT(OFFSET => -60*5);  -- 5 minutes ago
```

---

*Back to: [[Index|Tools Home]]*
