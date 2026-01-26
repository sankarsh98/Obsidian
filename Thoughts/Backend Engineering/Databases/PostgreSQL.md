# PostgreSQL

> *The world's most advanced open source database*

---

## Why PostgreSQL?

- **ACID compliant** - Reliable transactions
- **Feature-rich** - JSON, arrays, full-text search
- **Extensible** - Custom types, functions
- **Standards** - SQL compliant

---

## Basic Operations

### Create Tables

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    total DECIMAL(10, 2),
    status VARCHAR(50) DEFAULT 'pending'
);
```

### CRUD Operations

```sql
-- Create
INSERT INTO users (name, email) VALUES ('John', 'john@example.com');

-- Read
SELECT * FROM users WHERE email = 'john@example.com';

-- Update
UPDATE users SET name = 'John Doe' WHERE id = 1;

-- Delete
DELETE FROM users WHERE id = 1;
```

### Joins

```sql
SELECT u.name, o.id as order_id, o.total
FROM users u
JOIN orders o ON u.id = o.user_id
WHERE o.status = 'completed';
```

---

## Advanced Features

### JSON Support

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    metadata JSONB
);

-- Query JSON
SELECT * FROM products 
WHERE metadata->>'category' = 'electronics';

-- Index JSON
CREATE INDEX idx_metadata ON products USING GIN (metadata);
```

### Full-Text Search

```sql
-- Add search column
ALTER TABLE products ADD COLUMN search_vector tsvector;

-- Search
SELECT * FROM products 
WHERE search_vector @@ to_tsquery('laptop & gaming');
```

---

## Indexes

```sql
-- B-tree (default)
CREATE INDEX idx_email ON users(email);

-- Partial index
CREATE INDEX idx_active ON users(email) WHERE active = true;

-- Composite
CREATE INDEX idx_user_status ON orders(user_id, status);
```

---

*Back to: [[Index|Databases Home]]*
