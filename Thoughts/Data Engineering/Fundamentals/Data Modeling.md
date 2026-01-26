# Data Modeling

> *Designing data structures that scale*

---

## What is Data Modeling?

Data modeling is the process of **designing how data is organized, stored, and accessed**. Good data models enable:
- Fast queries
- Easy maintenance
- Clear business understanding
- Scalable architecture

---

## Key Concepts

### OLTP vs OLAP

| Aspect | OLTP | OLAP |
|--------|------|------|
| **Purpose** | Transactions | Analytics |
| **Queries** | Simple, frequent | Complex, less frequent |
| **Data** | Current | Historical |
| **Design** | Normalized (3NF) | Denormalized |
| **Example** | Order system DB | Data Warehouse |

---

## Dimensional Modeling (Kimball)

### Star Schema

```
                    ┌─────────────┐
                    │  dim_date   │
                    │─────────────│
                    │ date_key    │
                    │ date        │
                    │ day_of_week │
                    │ month       │
                    │ quarter     │
                    │ year        │
                    └──────┬──────┘
                           │
┌─────────────┐     ┌──────┴──────┐     ┌─────────────┐
│dim_customer │     │ fact_sales  │     │ dim_product │
│─────────────│     │─────────────│     │─────────────│
│ customer_key│────▶│ date_key    │◀────│ product_key │
│ customer_id │     │ customer_key│     │ product_id  │
│ name        │     │ product_key │     │ name        │
│ email       │     │ store_key   │     │ category    │
│ segment     │     │─────────────│     │ brand       │
└─────────────┘     │ quantity    │     └─────────────┘
                    │ revenue     │
                    │ discount    │
                    └──────┬──────┘
                           │
                    ┌──────┴──────┐
                    │  dim_store  │
                    └─────────────┘
```

### Fact Tables

**Types of facts:**
- **Additive**: Can be summed (revenue, quantity)
- **Semi-additive**: Sum across some dimensions (account balance)
- **Non-additive**: Cannot be summed (ratios, percentages)

```sql
CREATE TABLE fact_sales (
    date_key INT REFERENCES dim_date(date_key),
    customer_key INT REFERENCES dim_customer(customer_key),
    product_key INT REFERENCES dim_product(product_key),
    store_key INT REFERENCES dim_store(store_key),
    -- Measures
    quantity INT,
    revenue DECIMAL(10,2),
    discount DECIMAL(10,2),
    cost DECIMAL(10,2)
);
```

### Dimension Tables

```sql
CREATE TABLE dim_customer (
    customer_key SERIAL PRIMARY KEY,  -- Surrogate key
    customer_id VARCHAR(50),           -- Natural key
    name VARCHAR(100),
    email VARCHAR(100),
    segment VARCHAR(50),
    created_at TIMESTAMP,
    -- SCD Type 2 columns
    valid_from TIMESTAMP,
    valid_to TIMESTAMP,
    is_current BOOLEAN
);
```

---

## Slowly Changing Dimensions (SCD)

### Type 0: Retain Original
No changes allowed after initial load.

### Type 1: Overwrite
Simply update the value. No history.

```sql
UPDATE dim_customer 
SET email = 'new@email.com' 
WHERE customer_id = 'C001';
```

### Type 2: Add New Row (Most Common)
Track full history with versioning.

```sql
-- Mark old record as expired
UPDATE dim_customer SET 
    valid_to = CURRENT_TIMESTAMP,
    is_current = FALSE
WHERE customer_id = 'C001' AND is_current = TRUE;

-- Insert new record
INSERT INTO dim_customer (customer_id, email, valid_from, valid_to, is_current)
VALUES ('C001', 'new@email.com', CURRENT_TIMESTAMP, '9999-12-31', TRUE);
```

### Type 3: Add Column
Keep current and previous value only.

```sql
ALTER TABLE dim_customer ADD COLUMN previous_email VARCHAR(100);

UPDATE dim_customer SET 
    previous_email = email,
    email = 'new@email.com'
WHERE customer_id = 'C001';
```

---

## Normalization vs Denormalization

### Normalization (OLTP)

Reduces redundancy, ensures consistency.

**3NF Rules:**
1. No repeating groups
2. No partial dependencies
3. No transitive dependencies

### Denormalization (OLAP)

Adds redundancy for query performance.

```sql
-- Denormalized for analytics
CREATE TABLE sales_flat AS
SELECT 
    s.sale_id,
    s.sale_date,
    s.quantity,
    s.revenue,
    c.customer_name,
    c.segment,
    p.product_name,
    p.category,
    p.brand
FROM fact_sales s
JOIN dim_customer c ON s.customer_key = c.customer_key
JOIN dim_product p ON s.product_key = p.product_key;
```

---

## Modern Approaches

### One Big Table (OBT)

Single wide, denormalized table for simplicity.

**Pros:** Simple queries, no joins needed  
**Cons:** Storage, update complexity

### Data Vault

Designed for enterprise data integration.

**Components:**
- **Hubs**: Business keys
- **Links**: Relationships
- **Satellites**: Descriptive attributes

---

## Best Practices

1. **Use surrogate keys** - Integer keys for joins
2. **Include audit columns** - created_at, updated_at
3. **Document thoroughly** - Business definitions
4. **Consider query patterns** - Design for common queries
5. **Plan for growth** - Partition large tables

---

*Next: [[Distributed Systems|Distributed Systems →]]*

*Back to: [[Index|Fundamentals Home]]*
