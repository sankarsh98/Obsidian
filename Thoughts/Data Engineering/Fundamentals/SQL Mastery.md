# SQL Mastery

> *The language every Data Engineer must speak fluently*

---

## Why SQL Matters

SQL is the **universal language of data**. Every data engineer will:
- Query databases daily
- Transform data in warehouses
- Debug pipelines
- Communicate with analysts

---

## Learning Roadmap

```
Week 1: Basics → Week 2: JOINs → Week 3: Aggregations → Week 4: Window Functions → Week 5: Advanced
```

---

## Core Concepts

### 1. SELECT Fundamentals

```sql
-- Basic query structure
SELECT 
    column1,
    column2,
    column3
FROM table_name
WHERE condition
ORDER BY column1 DESC
LIMIT 100;
```

### 2. JOINs

```sql
-- INNER JOIN: Only matching rows
SELECT c.name, o.order_id
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id;

-- LEFT JOIN: All from left, matching from right
SELECT c.name, o.order_id
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id;

-- Multiple JOINs
SELECT c.name, o.order_id, p.product_name
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN products p ON o.product_id = p.id;
```

### 3. Aggregations

```sql
-- GROUP BY with HAVING
SELECT 
    category,
    COUNT(*) as order_count,
    SUM(amount) as total_revenue,
    AVG(amount) as avg_order_value
FROM orders
GROUP BY category
HAVING SUM(amount) > 10000
ORDER BY total_revenue DESC;
```

### 4. Window Functions 🔴 Critical

```sql
-- ROW_NUMBER: Unique ranking
SELECT 
    *,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) as rn
FROM orders;

-- Running totals
SELECT 
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) as running_total
FROM orders;

-- LAG/LEAD: Previous/Next values
SELECT 
    order_date,
    amount,
    LAG(amount, 1) OVER (ORDER BY order_date) as prev_amount,
    amount - LAG(amount, 1) OVER (ORDER BY order_date) as change
FROM orders;

-- RANK vs DENSE_RANK
SELECT 
    product_id,
    revenue,
    RANK() OVER (ORDER BY revenue DESC) as rank,
    DENSE_RANK() OVER (ORDER BY revenue DESC) as dense_rank
FROM product_sales;
```

### 5. CTEs (Common Table Expressions)

```sql
-- Simple CTE
WITH monthly_sales AS (
    SELECT 
        DATE_TRUNC('month', order_date) as month,
        SUM(amount) as revenue
    FROM orders
    GROUP BY 1
)
SELECT * FROM monthly_sales WHERE revenue > 100000;

-- Multiple CTEs
WITH 
customers_orders AS (
    SELECT customer_id, COUNT(*) as order_count
    FROM orders GROUP BY customer_id
),
high_value AS (
    SELECT customer_id FROM customers_orders 
    WHERE order_count > 10
)
SELECT c.* FROM customers c 
JOIN high_value h ON c.id = h.customer_id;
```

### 6. Subqueries

```sql
-- Scalar subquery
SELECT 
    *,
    (SELECT AVG(amount) FROM orders) as avg_amount
FROM orders;

-- IN subquery
SELECT * FROM customers
WHERE id IN (
    SELECT DISTINCT customer_id 
    FROM orders 
    WHERE amount > 1000
);

-- EXISTS
SELECT * FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o 
    WHERE o.customer_id = c.id 
    AND o.amount > 5000
);
```

---

## Practice Problems

### Easy (Warmup)
1. Select all customers from India
2. Count orders by status
3. Find top 10 products by revenue

### Medium
1. Find customers with no orders (LEFT JOIN + NULL)
2. Calculate month-over-month growth
3. Rank products within each category

### Hard
1. Find consecutive days of activity
2. Session analysis with gaps
3. Funnel conversion analysis

---

## Resources

- [LeetCode SQL](https://leetcode.com/problemset/database/)
- [HackerRank SQL](https://www.hackerrank.com/domains/sql)
- [Mode SQL Tutorial](https://mode.com/sql-tutorial/)
- [SQLZoo](https://sqlzoo.net/)

---

*Next: [[Python for Data Engineering|Python for DE →]]*

*Back to: [[Index|Fundamentals Home]]*
