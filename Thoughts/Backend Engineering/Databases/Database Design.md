# Database Design

> *Schema design principles*

---

## Normalization

### Normal Forms

| Form | Rule |
|------|------|
| **1NF** | Atomic values, no repeating groups |
| **2NF** | 1NF + no partial dependencies |
| **3NF** | 2NF + no transitive dependencies |

### Example

```sql
-- Normalized (3NF)
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE addresses (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    street VARCHAR(200),
    city VARCHAR(100)
);

-- Denormalized (for performance)
CREATE TABLE users_denorm (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    address_street VARCHAR(200),
    address_city VARCHAR(100)
);
```

---

## Relationships

### One-to-Many

```sql
-- Users have many orders
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    total DECIMAL(10, 2)
);
```

### Many-to-Many

```sql
-- Users have many roles, roles have many users
CREATE TABLE user_roles (
    user_id INTEGER REFERENCES users(id),
    role_id INTEGER REFERENCES roles(id),
    PRIMARY KEY (user_id, role_id)
);
```

### One-to-One

```sql
CREATE TABLE user_profiles (
    user_id INTEGER PRIMARY KEY REFERENCES users(id),
    bio TEXT,
    avatar_url VARCHAR(255)
);
```

---

## Best Practices

1. **Use appropriate data types** - INT vs BIGINT, VARCHAR vs TEXT
2. **Add indexes** on frequently queried columns
3. **Use foreign keys** for referential integrity
4. **Consider read/write patterns** before design
5. **Plan for growth** - partition strategy

---

## Schema Migrations

```sql
-- Add column
ALTER TABLE users ADD COLUMN phone VARCHAR(20);

-- Add index
CREATE INDEX idx_users_email ON users(email);

-- Remove column (careful!)
ALTER TABLE users DROP COLUMN old_field;
```

---

*Back to: [[Index|Databases Home]]*
