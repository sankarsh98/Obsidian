# System Design Basics

> *Designing scalable, reliable systems*

---

## Key Concepts

### Scalability

| Type | Description | Example |
|------|-------------|---------|
| **Vertical** | Bigger machine | More RAM, CPU |
| **Horizontal** | More machines | Add servers |

---

## Load Balancing

```
           ┌─────────────────┐
           │  Load Balancer  │
           └────────┬────────┘
                    │
      ┌─────────────┼─────────────┐
      ▼             ▼             ▼
┌──────────┐  ┌──────────┐  ┌──────────┐
│ Server 1 │  │ Server 2 │  │ Server 3 │
└──────────┘  └──────────┘  └──────────┘
```

**Algorithms:**
- Round Robin
- Least Connections
- IP Hash
- Weighted

---

## Caching

### Cache Strategies

| Strategy | When to Use |
|----------|-------------|
| **Cache-Aside** | Read-heavy, can tolerate stale |
| **Write-Through** | Strong consistency needed |
| **Write-Behind** | High write throughput |

### Where to Cache

```
Client → CDN → API Gateway → Application Cache → Database
  │       │         │              │
  └───────┴─────────┴──────────────┘
         Multiple cache layers
```

---

## Database Scaling

### Read Replicas
```
        ┌─────────────┐
        │   Primary   │ ← Writes
        │  (Master)   │
        └──────┬──────┘
               │ Replication
    ┌──────────┼──────────┐
    ▼          ▼          ▼
┌────────┐ ┌────────┐ ┌────────┐
│Replica1│ │Replica2│ │Replica3│  ← Reads
└────────┘ └────────┘ └────────┘
```

### Sharding
```
┌──────────────────────────────────────────┐
│              Shard Router                 │
└─────────────────┬────────────────────────┘
                  │
    ┌─────────────┼─────────────┐
    ▼             ▼             ▼
┌────────┐   ┌────────┐   ┌────────┐
│Shard A │   │Shard B │   │Shard C │
│(A-H)   │   │(I-P)   │   │(Q-Z)   │
└────────┘   └────────┘   └────────┘
```

---

## CAP Theorem

Choose 2 of 3:
- **C**onsistency: All nodes see same data
- **A**vailability: Every request gets response
- **P**artition Tolerance: System works despite network issues

| System | Choice |
|--------|--------|
| PostgreSQL | CP |
| MongoDB | CP/AP (configurable) |
| Cassandra | AP |

---

## Message Queues

```
┌──────────┐     ┌─────────────┐     ┌──────────┐
│ Producer │────▶│   Queue     │────▶│ Consumer │
└──────────┘     │  (RabbitMQ) │     └──────────┘
                 └─────────────┘
```

**Use Cases:**
- Async processing
- Decoupling services
- Rate limiting
- Retry logic

---

## API Rate Limiting

| Algorithm | Description |
|-----------|-------------|
| Token Bucket | Smooth traffic |
| Sliding Window | Accurate limiting |
| Fixed Window | Simple, bursty |

---

*Next: [[Design Patterns|Design Patterns →]]*

*Back to: [[Index|Fundamentals Home]]*
