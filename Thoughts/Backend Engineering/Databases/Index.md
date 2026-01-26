# Databases Index

> *Data storage technologies*

---

## Database Types

| Type | Examples | Best For |
|------|----------|----------|
| **Relational** | PostgreSQL, MySQL | Structured data, ACID |
| **Document** | MongoDB | Flexible schemas |
| **Key-Value** | Redis | Caching, sessions |
| **Time-Series** | InfluxDB | Metrics, IoT |
| **Graph** | Neo4j | Relationships |

---

## Core Topics

### [[PostgreSQL]]
The go-to relational database.

### [[MongoDB]]
Flexible document database.

### [[Redis]]
In-memory caching and more.

### [[Database Design]]
Schema design principles.

---

## Choosing a Database

```
Need ACID transactions? → PostgreSQL
Flexible schema? → MongoDB
Fast caching? → Redis
Complex relationships? → PostgreSQL or Neo4j
High write throughput? → Cassandra
```

---

## Database Comparison

| Feature | PostgreSQL | MongoDB | Redis |
|---------|------------|---------|-------|
| **Type** | Relational | Document | Key-Value |
| **Schema** | Fixed | Flexible | None |
| **ACID** | Full | Configurable | Limited |
| **Scale** | Vertical | Horizontal | Horizontal |
| **Best For** | Most apps | Rapid iteration | Caching |

---

*Next: [[../Tools/Index|Tools →]]*

*Back to: [[../Index|Backend Engineering Home]]*
