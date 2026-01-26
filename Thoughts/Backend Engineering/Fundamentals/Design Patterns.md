# Design Patterns

> *Reusable solutions to common problems*

---

## Architectural Patterns

### MVC (Model-View-Controller)

```
┌───────────┐     ┌────────────┐     ┌───────────┐
│   View    │◀────│ Controller │────▶│   Model   │
│  (UI/API) │     │  (Logic)   │     │  (Data)   │
└───────────┘     └────────────┘     └───────────┘
```

### Repository Pattern

```python
# Abstract away data access
class UserRepository:
    def find_by_id(self, id: int) -> User:
        return self.db.query(User).filter_by(id=id).first()
    
    def save(self, user: User) -> User:
        self.db.add(user)
        self.db.commit()
        return user
```

---

## Creational Patterns

### Factory Pattern

```python
class NotificationFactory:
    @staticmethod
    def create(type: str) -> Notification:
        if type == "email":
            return EmailNotification()
        elif type == "sms":
            return SMSNotification()
        elif type == "push":
            return PushNotification()

# Usage
notification = NotificationFactory.create("email")
notification.send(message)
```

### Singleton Pattern

```python
class Database:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.connection = create_connection()
        return cls._instance

# Same instance everywhere
db1 = Database()
db2 = Database()
assert db1 is db2
```

---

## Behavioral Patterns

### Observer Pattern

```python
class EventEmitter:
    def __init__(self):
        self._subscribers = {}
    
    def subscribe(self, event: str, callback):
        self._subscribers.setdefault(event, []).append(callback)
    
    def emit(self, event: str, data):
        for callback in self._subscribers.get(event, []):
            callback(data)

# Usage
events = EventEmitter()
events.subscribe("user_created", send_welcome_email)
events.subscribe("user_created", create_default_settings)
events.emit("user_created", user)
```

### Strategy Pattern

```python
class PaymentProcessor:
    def __init__(self, strategy):
        self.strategy = strategy
    
    def process(self, amount):
        return self.strategy.pay(amount)

class CreditCardPayment:
    def pay(self, amount):
        return f"Paid ${amount} via Credit Card"

class PayPalPayment:
    def pay(self, amount):
        return f"Paid ${amount} via PayPal"

# Usage
processor = PaymentProcessor(CreditCardPayment())
processor.process(100)
```

---

## Dependency Injection

```python
# Without DI (tightly coupled)
class UserService:
    def __init__(self):
        self.db = PostgresDatabase()  # Hard dependency

# With DI (loosely coupled)
class UserService:
    def __init__(self, db: Database):
        self.db = db  # Injected dependency

# Easy to test and swap
service = UserService(MockDatabase())
```

---

## When to Use

| Pattern | Use Case |
|---------|----------|
| **Repository** | Data access abstraction |
| **Factory** | Complex object creation |
| **Singleton** | Shared resources (DB, config) |
| **Observer** | Event-driven systems |
| **Strategy** | Swappable algorithms |
| **DI** | Testability, flexibility |

---

*Back to: [[Index|Fundamentals Home]]*
