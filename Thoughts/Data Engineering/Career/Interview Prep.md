# Interview Preparation

> *Prepare for Data Engineering interviews*

---

## Interview Structure

| Round | Focus | Duration |
|-------|-------|----------|
| **Phone Screen** | Basics, motivation | 30-45 min |
| **Technical 1** | SQL + Python coding | 60 min |
| **Technical 2** | System Design | 60 min |
| **Behavioral** | Experience, culture | 45 min |
| **Hiring Manager** | Team fit, growth | 30 min |

---

## SQL Questions

### Common Patterns

```sql
-- 1. Second highest salary
SELECT MAX(salary) FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- 2. Running total
SELECT date, amount,
    SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;

-- 3. Duplicate detection
SELECT email, COUNT(*) FROM users
GROUP BY email HAVING COUNT(*) > 1;

-- 4. Top N per group
WITH ranked AS (
    SELECT *, ROW_NUMBER() OVER (
        PARTITION BY department ORDER BY salary DESC
    ) as rn FROM employees
)
SELECT * FROM ranked WHERE rn <= 3;

-- 5. Gap detection (consecutive days)
SELECT *,
    date - LAG(date) OVER (ORDER BY date) as gap
FROM user_logins;
```

---

## Python Questions

### Common Patterns

```python
# 1. Flatten nested JSON
def flatten_json(nested, prefix=''):
    flat = {}
    for key, value in nested.items():
        new_key = f"{prefix}.{key}" if prefix else key
        if isinstance(value, dict):
            flat.update(flatten_json(value, new_key))
        else:
            flat[new_key] = value
    return flat

# 2. Deduplicate while preserving order
def dedupe(items):
    seen = set()
    result = []
    for item in items:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

# 3. Chunk a list
def chunk(lst, size):
    return [lst[i:i + size] for i in range(0, len(lst), size)]
```

---

## System Design Questions

### Common Topics

1. **Design a data pipeline** for e-commerce analytics
2. **Design a real-time dashboard** for metrics
3. **Design a recommendation system** data layer
4. **Design a log analytics** platform
5. **Design a data warehouse** for a startup

### Framework (RADIO)

1. **R**equirements - Clarify scope
2. **A**rchitecture - High-level design
3. **D**ata Model - Schema design
4. **I**nterfaces - APIs, connections
5. **O**perations - Monitoring, maintenance

### Example: E-commerce Pipeline

```
Requirements:
- Process 10M orders/day
- Real-time inventory updates
- Daily sales reports
- Historical analytics

Architecture:
┌────────────┐     ┌─────────┐     ┌──────────┐
│ PostgreSQL │────▶│  Kafka  │────▶│  Spark   │
│ (Orders)   │     │         │     │Streaming │
└────────────┘     └─────────┘     └────┬─────┘
                                        │
                        ┌───────────────┴───────────────┐
                        ▼                               ▼
                 ┌─────────────┐                 ┌─────────────┐
                 │    S3       │                 │   Redis     │
                 │ (Data Lake) │                 │ (Real-time) │
                 └──────┬──────┘                 └─────────────┘
                        │
                 ┌──────▼──────┐
                 │  Snowflake  │
                 │ (Warehouse) │
                 └─────────────┘
```

---

## Behavioral Questions

### STAR Method

- **S**ituation: Context
- **T**ask: Your responsibility
- **A**ction: What you did
- **R**esult: Outcome

### Common Questions

1. Tell me about a challenging pipeline you built
2. How do you handle data quality issues?
3. Describe a time you disagreed with a teammate
4. How do you prioritize when everything is urgent?
5. What's your biggest technical mistake?

---

## Study Plan (2 Weeks)

| Day | Focus |
|-----|-------|
| 1-3 | SQL practice (50 problems) |
| 4-5 | Python coding (Data structures) |
| 6-7 | System design (2 problems) |
| 8-9 | Spark/Airflow review |
| 10 | Behavioral prep (5 stories) |
| 11-12 | Mock interviews |
| 13-14 | Review weak areas |

---

*Back to: [[Index|Career Home]]*
