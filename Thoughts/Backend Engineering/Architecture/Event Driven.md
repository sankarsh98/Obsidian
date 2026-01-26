# Event-Driven Architecture

> *Decoupled, reactive systems*

---

## What is EDA?

Systems communicate through events rather than direct calls.

```
┌──────────┐     ┌─────────────┐     ┌──────────┐
│ Producer │────▶│ Event Broker│────▶│ Consumer │
│          │     │ (Kafka/RMQ) │     │          │
└──────────┘     └─────────────┘     └──────────┘
```

---

## Event Types

| Type | Example |
|------|---------|
| **Domain Event** | UserCreated, OrderPlaced |
| **Command** | CreateUser, ProcessPayment |
| **Query** | GetUserById |

---

## Benefits

- **Decoupling** - Services don't know each other
- **Scalability** - Independent scaling
- **Resilience** - Async retries
- **Audit** - Event log

---

## Example Flow

```
User Signup → UserCreated Event
                   │
    ┌──────────────┼──────────────┐
    ▼              ▼              ▼
Send Email    Create Profile   Analytics
```

```python
# Publisher
def create_user(data):
    user = db.save(User(**data))
    events.publish("user.created", {
        "id": user.id,
        "email": user.email
    })

# Subscribers
@events.subscribe("user.created")
def send_welcome_email(event):
    email.send(event["email"], "Welcome!")

@events.subscribe("user.created")
def create_default_settings(event):
    settings.create(user_id=event["id"])
```

---

*Back to: [[Index|Architecture Home]]*
