# Basic Data Warehouse Project

> *Your first star schema implementation*

---

## Project Overview

Design a simple star schema for an e-commerce system.

---

## Schema Design

```
                    ┌─────────────┐
                    │  dim_date   │
                    └──────┬──────┘
                           │
┌─────────────┐     ┌──────┴──────┐     ┌─────────────┐
│dim_customer │────▶│ fact_sales  │◀────│ dim_product │
└─────────────┘     └─────────────┘     └─────────────┘
```

---

## SQL Implementation

```sql
-- Dimension: Customers
CREATE TABLE dim_customer (
    customer_key SERIAL PRIMARY KEY,
    customer_id VARCHAR(50),
    name VARCHAR(100),
    email VARCHAR(100),
    segment VARCHAR(50)
);

-- Dimension: Products
CREATE TABLE dim_product (
    product_key SERIAL PRIMARY KEY,
    product_id VARCHAR(50),
    name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2)
);

-- Dimension: Date
CREATE TABLE dim_date (
    date_key INT PRIMARY KEY,
    date DATE,
    day_of_week VARCHAR(10),
    month INT,
    year INT
);

-- Fact: Sales
CREATE TABLE fact_sales (
    sale_id SERIAL PRIMARY KEY,
    customer_key INT REFERENCES dim_customer,
    product_key INT REFERENCES dim_product,
    date_key INT REFERENCES dim_date,
    quantity INT,
    revenue DECIMAL(10,2)
);
```

---

*Back to: [[Index|Projects Home]]*
