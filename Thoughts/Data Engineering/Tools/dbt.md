# dbt (Data Build Tool)

> *Transform data in your warehouse using SQL*

---

## What is dbt?

dbt is a **transformation tool** that enables data analysts and engineers to transform data in their warehouse using **SQL SELECT statements**.

---

## Core Concepts

### Project Structure

```
my_dbt_project/
├── dbt_project.yml
├── models/
│   ├── staging/
│   │   ├── stg_customers.sql
│   │   └── stg_orders.sql
│   └── marts/
│       ├── dim_customers.sql
│       └── fact_orders.sql
├── tests/
├── macros/
└── seeds/
```

### Materializations

| Type | Description | Use Case |
|------|-------------|----------|
| **view** | Virtual table | Light transforms |
| **table** | Physical table | Final outputs |
| **incremental** | Append/merge new data | Large tables |
| **ephemeral** | CTE only | Intermediate logic |

---

## Building Models

### Basic Model

```sql
-- models/staging/stg_customers.sql
SELECT
    id as customer_id,
    first_name,
    last_name,
    email,
    created_at
FROM {{ source('raw', 'customers') }}
WHERE deleted_at IS NULL
```

### With Config

```sql
-- models/marts/dim_customers.sql
{{
    config(
        materialized='table',
        schema='analytics'
    )
}}

SELECT
    customer_id,
    first_name || ' ' || last_name as full_name,
    email,
    created_at,
    DATEDIFF(day, created_at, CURRENT_DATE) as days_since_signup
FROM {{ ref('stg_customers') }}
```

### Incremental Model

```sql
-- models/marts/fact_orders.sql
{{
    config(
        materialized='incremental',
        unique_key='order_id'
    )
}}

SELECT
    order_id,
    customer_id,
    order_date,
    total_amount
FROM {{ ref('stg_orders') }}

{% if is_incremental() %}
WHERE order_date > (SELECT MAX(order_date) FROM {{ this }})
{% endif %}
```

---

## Key Features

### Sources

```yaml
# models/staging/sources.yml
version: 2

sources:
  - name: raw
    database: raw_database
    schema: public
    tables:
      - name: customers
      - name: orders
        freshness:
          warn_after: {count: 24, period: hour}
```

### Tests

```yaml
# models/staging/schema.yml
version: 2

models:
  - name: stg_customers
    columns:
      - name: customer_id
        tests:
          - not_null
          - unique
      - name: email
        tests:
          - not_null
```

### Macros

```sql
-- macros/cents_to_dollars.sql
{% macro cents_to_dollars(column_name) %}
    ({{ column_name }} / 100)::decimal(10,2)
{% endmacro %}

-- Usage in model
SELECT
    order_id,
    {{ cents_to_dollars('amount_cents') }} as amount_dollars
FROM orders
```

### Jinja Templating

```sql
-- Control flow
{% if target.name == 'prod' %}
    SELECT * FROM prod_table
{% else %}
    SELECT * FROM {{ ref('dev_table') }} LIMIT 1000
{% endif %}

-- Loops
SELECT
{% for payment_type in ['credit', 'debit', 'cash'] %}
    SUM(CASE WHEN type = '{{ payment_type }}' THEN amount ELSE 0 END) as {{ payment_type }}_total
    {% if not loop.last %},{% endif %}
{% endfor %}
FROM payments
```

---

## Commands

```bash
# Run all models
dbt run

# Run specific model
dbt run --select dim_customers

# Run with dependencies
dbt run --select +dim_customers  # upstream
dbt run --select dim_customers+  # downstream

# Test
dbt test

# Generate docs
dbt docs generate
dbt docs serve

# Debug
dbt debug
```

---

## Best Practices

1. **Layer your models**: staging → intermediate → marts
2. **Use refs**: `{{ ref('model') }}` not hardcoded tables
3. **Test everything**: unique, not_null at minimum
4. **Document models**: Add descriptions
5. **Keep models simple**: One purpose per model

---

*Next: [[Cloud Platforms|Cloud →]]*

*Back to: [[Index|Tools Home]]*
