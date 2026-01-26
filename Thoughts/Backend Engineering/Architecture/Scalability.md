# Scalability Patterns

> *Handling growth*

---

## Scaling Strategies

| Strategy | How | When |
|----------|-----|------|
| **Vertical** | Bigger machine | Quick fix |
| **Horizontal** | More machines | Long-term |

---

## Key Patterns

### Load Balancing

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

### Caching

```
Request → Check Cache → Hit? Return
                ↓
              Miss → Query DB → Cache → Return
```

### Database Replication

```
Writes → Primary → Replicate
Reads  → Replicas
```

### Sharding

Split data across databases by key (user_id, region).

---

## Numbers to Know

| Metric | Target |
|--------|--------|
| API response | < 200ms |
| Page load | < 3s |
| Uptime | 99.9% (8.7h/year down) |
| Database query | < 50ms |

---

*Back to: [[Index|Architecture Home]]*
