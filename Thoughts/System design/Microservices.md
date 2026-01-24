# Microservices

> An architectural approach where applications are built as a collection of small, independent services.

---

## Back to [[System Design]]

---

## What are Microservices?

Microservices architecture structures an application as a collection of loosely coupled, independently deployable services. Each service is focused on a specific business capability.

```
Monolith:                      Microservices:
+------------------+           +-------+  +-------+  +-------+
|                  |           | User  |  | Order |  |Payment|
|   All Features   |    -->    |Service|  |Service|  |Service|
|   One Codebase   |           +-------+  +-------+  +-------+
|   One Database   |               |          |          |
+------------------+           +-------+  +-------+  +-------+
                               |User DB|  |Order DB|  |Pay DB |
                               +-------+  +-------+  +-------+
```

---

## Monolith vs Microservices

| Aspect | Monolith | Microservices |
|--------|----------|---------------|
| Deployment | All or nothing | Independent |
| Scaling | Entire app | Per service |
| Technology | Single stack | Polyglot |
| Team Structure | One team | Multiple teams |
| Complexity | Lower initially | Higher initially |
| Data | Shared DB | DB per service |
| Failure Impact | Entire app | Isolated |

---

## When to Use Microservices

### Good Fit
- Large, complex applications
- Multiple development teams
- Need independent scaling
- Diverse technology requirements
- High availability requirements

### Poor Fit
- Small applications
- Small teams
- Unclear domain boundaries
- Tight deadlines
- Limited DevOps expertise

---

## Core Principles

### 1. Single Responsibility
Each service does one thing well.

```
❌ OrderService that handles orders, payments, and shipping
✓ OrderService, PaymentService, ShippingService
```

### 2. Loose Coupling
Services are independent, minimal dependencies.

```
❌ Direct database access between services
✓ API calls or messaging between services
```

### 3. High Cohesion
Related functionality grouped together.

```
User Service:
- User registration
- User authentication
- User profile management
```

### 4. Database per Service
Each service owns its data.

```
Order Service --> Order DB (only Order Service can access)
User Service  --> User DB (only User Service can access)
```

---

## Service Communication

### Synchronous Communication

#### REST APIs
```
Order Service                    User Service
     |                               |
     |  GET /users/123               |
     |------------------------------>|
     |                               |
     |  { "id": 123, "name": "John"} |
     |<------------------------------|
```

#### gRPC
```protobuf
service UserService {
  rpc GetUser(UserRequest) returns (User);
}

message UserRequest {
  int32 id = 1;
}

message User {
  int32 id = 1;
  string name = 2;
}
```

### Asynchronous Communication

#### Message Queues
```
Order Service --> [Message Queue] --> Payment Service
                                  --> Inventory Service
                                  --> Notification Service
```

#### Event-Driven
```
Order Created Event
        |
        v
+------------------+
|   Event Bus      |
+------------------+
    |    |    |
    v    v    v
Payment Inventory Email
Service Service   Service
```

### Comparison

| Pattern | Use Case | Pros | Cons |
|---------|----------|------|------|
| REST | Simple CRUD | Easy, standard | Coupling, latency |
| gRPC | High performance | Fast, typed | Complexity |
| Message Queue | Async processing | Decoupled | Eventual consistency |
| Events | Event sourcing | Loose coupling | Debugging harder |

---

## Service Discovery

### Client-Side Discovery

```
+--------+     1. Query      +------------+
| Client | ----------------> | Service    |
+--------+                   | Registry   |
    |                        +------------+
    | 2. Get instances list       ^
    v                             |
+--------+                   3. Register
| Call   | ------------------+
| Service|                   |
+--------+              +---------+
                        | Service |
                        | Instance|
                        +---------+
```

### Server-Side Discovery

```
+--------+     1. Request    +-------------+
| Client | ----------------> | Load        |
+--------+                   | Balancer    |
                             +-------------+
                                   |
                    2. Query Registry
                                   |
                             +-------------+
                             | Service     |
                             | Registry    |
                             +-------------+
                                   |
                    3. Route to instance
                                   v
                             +---------+
                             | Service |
                             +---------+
```

### Tools
- **Consul** - Service mesh, key-value store
- **etcd** - Distributed key-value store
- **Eureka** - Netflix OSS service registry
- **Kubernetes** - Built-in service discovery

---

## API Gateway

### What is an API Gateway?

Single entry point for all client requests.

```
              +-------------+
              | API Gateway |
              +-------------+
               /     |     \
              v      v      v
         +------+ +------+ +------+
         |User  | |Order | |Product|
         |Svc   | |Svc   | |Svc    |
         +------+ +------+ +------+
```

### Responsibilities

```
+----------------------------------+
|           API Gateway            |
+----------------------------------+
| - Authentication/Authorization   |
| - Rate Limiting                  |
| - Request Routing                |
| - Load Balancing                 |
| - Caching                        |
| - Request/Response Transform     |
| - SSL Termination                |
| - Monitoring/Logging             |
+----------------------------------+
```

### Popular API Gateways
- Kong
- AWS API Gateway
- Nginx
- Envoy
- Traefik

---

## Patterns

### Circuit Breaker

Prevent cascade failures.

```
States:
+--------+  Failures  +------+  Timeout  +-----------+
| Closed | ---------> | Open | --------> | Half-Open |
+--------+            +------+           +-----------+
    ^                                         |
    |              Success                    |
    +-----------------------------------------+
```

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failures = 0
        self.state = "CLOSED"
        self.threshold = failure_threshold
        self.timeout = timeout
    
    def call(self, func):
        if self.state == "OPEN":
            if time_since_opened > self.timeout:
                self.state = "HALF_OPEN"
            else:
                raise CircuitOpenError()
        
        try:
            result = func()
            self.on_success()
            return result
        except Exception as e:
            self.on_failure()
            raise e
```

### Saga Pattern

Manage distributed transactions.

```
Order Saga:
1. Create Order (Order Service)
2. Reserve Inventory (Inventory Service)
3. Process Payment (Payment Service)
4. Ship Order (Shipping Service)

If step 3 fails:
- Compensate step 2 (Release Inventory)
- Compensate step 1 (Cancel Order)
```

#### Choreography
```
Order Created --> Inventory Reserved --> Payment Processed --> Order Shipped
     |                  |                      |
     v                  v                      v
(if fail)          (if fail)              (if fail)
Cancel Order    Release Inventory      Refund Payment
```

#### Orchestration
```
+------------+
| Saga       |
| Orchestrator|
+------------+
  |   |   |
  v   v   v
Order Inventory Payment
Svc   Svc       Svc
```

### Strangler Fig Pattern

Gradually migrate from monolith to microservices.

```
Phase 1: Monolith handles everything
+------------------+
|    Monolith      |
+------------------+

Phase 2: New features as microservices
+------------------+     +-------------+
|    Monolith      | --> | New Service |
+------------------+     +-------------+

Phase 3: Gradually extract services
+--------+     +------+  +------+  +------+
|Legacy  | --> |Svc A |  |Svc B |  |Svc C |
|(shrink)|     +------+  +------+  +------+
+--------+

Phase 4: Monolith fully replaced
+------+  +------+  +------+  +------+
|Svc A |  |Svc B |  |Svc C |  |Svc D |
+------+  +------+  +------+  +------+
```

### Sidecar Pattern

Deploy supporting features alongside service.

```
+---------------------------+
| Pod                       |
|  +-------+    +--------+  |
|  |Service| <->|Sidecar |  |
|  +-------+    +--------+  |
|                  |        |
|              Logging      |
|              Monitoring   |
|              Proxy        |
+---------------------------+
```

---

## Data Management

### Database per Service

```
+-------------+     +-------------+     +-------------+
|   Order     |     |   User      |     |   Product   |
|   Service   |     |   Service   |     |   Service   |
+-------------+     +-------------+     +-------------+
      |                   |                   |
+-------------+     +-------------+     +-------------+
|  Order DB   |     |   User DB   |     | Product DB  |
| (PostgreSQL)|     |   (MySQL)   |     | (MongoDB)   |
+-------------+     +-------------+     +-------------+
```

### Data Consistency Patterns

#### Event Sourcing
```
Events:
1. OrderCreated { orderId: 1, items: [...] }
2. ItemAdded { orderId: 1, item: {...} }
3. OrderPaid { orderId: 1, amount: 100 }
4. OrderShipped { orderId: 1, tracking: "..." }

Current state = replay all events
```

#### CQRS (Command Query Responsibility Segregation)
```
              Write               Read
                |                   |
                v                   v
+-------------+     sync      +-------------+
| Write Model | ------------> | Read Model  |
| (normalized)|               |(denormalized)|
+-------------+               +-------------+
```

---

## Deployment Strategies

### Blue-Green Deployment
```
Traffic --> Blue (current)
            Green (new) <-- Deploy here
            
Switch:
Traffic --> Green (now current)
            Blue (now standby)
```

### Canary Deployment
```
Traffic --> 95% --> Current Version
        --> 5%  --> New Version (canary)
        
Gradually increase canary percentage
```

### Rolling Deployment
```
Instance 1: v1 --> v2 (updated first)
Instance 2: v1 --> v1 --> v2 (updated second)
Instance 3: v1 --> v1 --> v1 --> v2 (updated third)
```

---

## Observability

### Three Pillars

```
+------------------------------------------+
|              Observability               |
+------------------------------------------+
     |              |              |
+--------+    +---------+    +----------+
| Logs   |    | Metrics |    | Traces   |
+--------+    +---------+    +----------+
What        How system      Request flow
happened    is performing   across services
```

### Distributed Tracing
```
Request ID: abc-123

User Service [====]
                  \
                   Order Service [========]
                                         \
                                          Payment Service [====]
                                          
Timeline: 0ms -------- 50ms -------- 150ms -------- 200ms
```

### Tools
- **Logging:** ELK Stack, Loki
- **Metrics:** Prometheus, Grafana
- **Tracing:** Jaeger, Zipkin

---

## Best Practices

1. **Design for failure** - Services will fail
2. **Embrace eventual consistency** - Don't fight distributed systems
3. **Automate everything** - CI/CD, testing, deployment
4. **Monitor extensively** - Can't fix what you can't see
5. **Keep services small** - 2-pizza team rule
6. **Define clear APIs** - Contract-first development
7. **Use containers** - Docker + Kubernetes

---

## Related Topics
- [[API Design]] - Designing service interfaces
- [[Message Queues]] - Async communication
- [[Distributed Systems]] - Understanding distributed challenges

---

## Tags
#microservices #architecture #distributed-systems #containers #system-design
