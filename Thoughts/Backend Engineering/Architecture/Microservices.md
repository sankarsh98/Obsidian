# Microservices

> *Distributed service architecture*

---

## What are Microservices?

Small, independent services that communicate over a network.

```
┌────────────┐   ┌────────────┐   ┌────────────┐
│   Users    │   │   Orders   │   │  Payments  │
│  Service   │   │  Service   │   │  Service   │
└─────┬──────┘   └─────┬──────┘   └─────┬──────┘
      │                │                │
      └────────────────┼────────────────┘
                       │
              ┌────────▼────────┐
              │   API Gateway   │
              └─────────────────┘
```

---

## Pros vs Cons

| Pros | Cons |
|------|------|
| Independent deployment | Network complexity |
| Technology freedom | Distributed debugging |
| Team autonomy | Data consistency |
| Scaling granularity | Operational overhead |

---

## Communication Patterns

### Synchronous (REST/gRPC)

```python
# Service A calls Service B
response = requests.get("http://service-b/api/users/123")
```

### Asynchronous (Message Queue)

```python
# Service A publishes event
queue.publish("user.created", {"id": 123, "email": "..."})

# Service B consumes
@queue.subscribe("user.created")
def handle_user_created(event):
    send_welcome_email(event["email"])
```

---

## Design Principles

1. **Single Responsibility** - One business capability per service
2. **Database per Service** - No shared databases
3. **API First** - Design contracts before code
4. **Failure Isolation** - Circuit breakers

---

*Back to: [[Index|Architecture Home]]*
