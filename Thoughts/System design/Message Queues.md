# Message Queues

> Asynchronous communication between services using message brokers.

---

## Back to [[System Design]]

---

## What are Message Queues?

Message queues provide asynchronous communication between services. A producer sends messages to a queue, and consumers process them independently.

```
Synchronous:
Producer --> Consumer (blocks until done)

Asynchronous:
Producer --> Queue --> Consumer (decoupled)
    |          |
    v          v
 Returns    Processes
immediately  later
```

---

## Why Use Message Queues?

### 1. Decoupling
```
Without Queue:
Service A --> Service B (tight coupling)
              If B is down, A fails

With Queue:
Service A --> Queue --> Service B
              If B is down, messages wait
```

### 2. Scalability
```
              +------------+
              | Consumer 1 |
              +------------+
                   ^
                   |
Producer --> Queue-+
                   |
                   v
              +------------+
              | Consumer N |
              +------------+
              
Add more consumers as load increases
```

### 3. Load Leveling
```
Traffic Spike:
              |    |
Requests: ----|----|----|----
              Peak

Queue absorbs spike:
Queue size: --|----|----|---- (grows during peak)
Processing:   -------|------- (steady rate)
```

### 4. Resilience
```
Consumer fails:
Producer --> Queue --> Consumer (fails)
                |
                v
            Message stays in queue
            Retry or process by another consumer
```

---

## Core Concepts

### Message
```json
{
  "id": "msg-123",
  "timestamp": "2024-01-15T10:30:00Z",
  "type": "order.created",
  "payload": {
    "orderId": "ord-456",
    "userId": "user-789",
    "total": 99.99
  },
  "metadata": {
    "correlationId": "req-abc",
    "retryCount": 0
  }
}
```

### Producer
```python
def create_order(order_data):
    # Save to database
    order = db.save(order_data)
    
    # Send message to queue
    queue.publish("orders", {
        "type": "order.created",
        "payload": order
    })
    
    return order
```

### Consumer
```python
def process_orders():
    while True:
        message = queue.consume("orders")
        
        if message.type == "order.created":
            # Process the order
            send_confirmation_email(message.payload)
            update_inventory(message.payload)
            
        # Acknowledge message
        queue.ack(message)
```

---

## Messaging Patterns

### 1. Point-to-Point (Queue)

One producer, one consumer per message.

```
Producer --> [Queue] --> Consumer
             Message consumed once
```

**Use Case:** Task distribution, work queues

### 2. Publish-Subscribe (Pub/Sub)

One producer, multiple consumers.

```
                    +--> Consumer A
                    |
Publisher --> Topic +--> Consumer B
                    |
                    +--> Consumer C
                    
All consumers receive all messages
```

**Use Case:** Event notification, broadcasting

### 3. Request-Reply

Synchronous-like pattern over queues.

```
+----------+     Request      +---------+
| Requester| ----------------> |         |
+----------+                   | Worker  |
     ^                         |         |
     |        Reply            +---------+
     +-------------------------+
     (via correlation ID)
```

### 4. Fan-Out

Distribute work across multiple workers.

```
                    +--> Worker 1 (messages 1, 4, 7...)
                    |
Producer --> Queue -+--> Worker 2 (messages 2, 5, 8...)
                    |
                    +--> Worker 3 (messages 3, 6, 9...)
```

### 5. Fan-In

Aggregate results from multiple sources.

```
Source A --+
           |
Source B --+--> Aggregator Queue --> Processor
           |
Source C --+
```

---

## Delivery Guarantees

### At-Most-Once
```
Producer --> Queue --> Consumer
              |
        (message may be lost)
        
- No retries
- Possible message loss
- Fast, simple
```

**Use Case:** Metrics, logs (loss acceptable)

### At-Least-Once
```
Producer --> Queue --> Consumer
              |           |
        (retry on failure)
        (may duplicate)
        
- Retries on failure
- Possible duplicates
- Consumer must be idempotent
```

**Use Case:** Most business operations

### Exactly-Once
```
Producer --> Queue --> Consumer
              |           |
        (complex coordination)
        (no loss, no duplicates)
        
- Requires distributed transactions
- Complex, slower
- Often simulated with at-least-once + idempotency
```

**Use Case:** Financial transactions, critical operations

---

## Message Ordering

### FIFO (First In, First Out)
```
Messages: [1, 2, 3, 4, 5]
Processed: 1, 2, 3, 4, 5 (in order)
```

**Guarantee:** Strict ordering within partition/queue

### Ordering Challenges
```
Multiple Consumers:
Queue: [1, 2, 3, 4, 5]
Consumer A processes: 1, 3, 5
Consumer B processes: 2, 4

Global order not guaranteed with parallel consumers
```

### Partition-Based Ordering
```
Partition Key: user_id

User 123 messages --> Partition 1 --> Consumer A (ordered)
User 456 messages --> Partition 2 --> Consumer B (ordered)
User 789 messages --> Partition 1 --> Consumer A (ordered)
```

---

## Dead Letter Queue (DLQ)

Handle messages that fail processing.

```
                         Success
Producer --> Main Queue ---------> Consumer
                  |
                  | Failure (after retries)
                  v
             Dead Letter Queue --> Alert/Manual Review
```

```python
def process_message(message):
    try:
        # Process message
        handle(message)
        queue.ack(message)
    except Exception as e:
        if message.retry_count < MAX_RETRIES:
            # Retry
            queue.nack(message, requeue=True)
        else:
            # Send to DLQ
            dlq.publish(message)
            queue.ack(message)
```

---

## Message Queue Technologies

### Comparison

| Feature | RabbitMQ | Kafka | AWS SQS | Redis Pub/Sub |
|---------|----------|-------|---------|---------------|
| Type | Message Broker | Event Streaming | Cloud Queue | In-memory |
| Ordering | Queue-level | Partition-level | FIFO option | None |
| Persistence | Yes | Yes | Yes | No |
| Throughput | Medium | Very High | High | Very High |
| Replay | No | Yes | No | No |
| Complexity | Medium | High | Low | Low |

### RabbitMQ

Traditional message broker with advanced routing.

```
+----------+     +----------+     +---------+
| Producer | --> | Exchange | --> | Queue 1 | --> Consumer
+----------+     +----------+     +---------+
                      |
                      +--> Queue 2 --> Consumer
                      
Exchange Types:
- Direct: Route by exact key match
- Topic: Route by pattern match
- Fanout: Broadcast to all queues
- Headers: Route by message headers
```

### Apache Kafka

Distributed event streaming platform.

```
+----------+     +-----------------+     +----------+
| Producer | --> | Topic           | --> | Consumer |
+----------+     | +-----------+   |     | Group A  |
                 | |Partition 0|   |     +----------+
                 | +-----------+   |
                 | |Partition 1|   |     +----------+
                 | +-----------+   | --> | Consumer |
                 | |Partition 2|   |     | Group B  |
                 | +-----------+   |     +----------+
                 +-----------------+
                 
- Partitions enable parallelism
- Consumer groups for scaling
- Messages retained for replay
```

### AWS SQS

Managed message queue service.

```
Standard Queue:
- At-least-once delivery
- Best-effort ordering
- Nearly unlimited throughput

FIFO Queue:
- Exactly-once processing
- Strict ordering
- 3,000 messages/sec (with batching)
```

---

## Implementation Patterns

### Idempotent Consumers

Handle duplicate messages safely.

```python
def process_order(message):
    order_id = message.payload.order_id
    
    # Check if already processed
    if db.exists(f"processed:{order_id}"):
        return  # Skip duplicate
    
    # Process order
    result = handle_order(message.payload)
    
    # Mark as processed
    db.set(f"processed:{order_id}", True)
    
    return result
```

### Transactional Outbox

Ensure database + message consistency.

```
+-----------+     +--------+     +-------+     +-------+
| Service   | --> | DB     | --> |Outbox | --> | Queue |
+-----------+     |        |     |Table  |     +-------+
                  +--------+     +-------+
                        |              ^
                        |   Transaction|
                        +--------------+
                        
1. Write business data
2. Write message to outbox table (same transaction)
3. Separate process publishes from outbox to queue
```

```sql
BEGIN TRANSACTION;
  INSERT INTO orders (id, ...) VALUES (...);
  INSERT INTO outbox (id, topic, payload) 
    VALUES ('...', 'orders', '{"orderId": ...}');
COMMIT;
```

### Competing Consumers

Scale processing with multiple workers.

```python
# Consumer 1, 2, 3... all run this code

def worker():
    while True:
        # Only one consumer gets each message
        message = queue.consume("tasks")
        
        try:
            process(message)
            queue.ack(message)
        except:
            queue.nack(message)  # Will be redelivered
```

---

## Message Design Best Practices

### Message Structure
```json
{
  "id": "unique-message-id",
  "type": "event.name",
  "version": "1.0",
  "timestamp": "ISO-8601",
  "source": "service-name",
  "correlationId": "trace-id",
  "payload": { ... },
  "metadata": { ... }
}
```

### Versioning
```json
// v1
{ "type": "user.created", "version": "1", "payload": { "name": "John" } }

// v2 (backward compatible)
{ "type": "user.created", "version": "2", "payload": { "name": "John", "email": "..." } }
```

### Message Size
```
Small messages (< 1KB): Queue directly
Large payloads (> 1MB): Store in S3, pass reference

{
  "type": "file.uploaded",
  "payload": {
    "fileRef": "s3://bucket/file-123.pdf",
    "size": 10485760
  }
}
```

---

## Monitoring

### Key Metrics

| Metric | Description | Alert When |
|--------|-------------|------------|
| Queue Depth | Messages waiting | Growing continuously |
| Processing Rate | Messages/second | Drops significantly |
| Consumer Lag | Behind producer | Increasing |
| Error Rate | Failed messages | Above threshold |
| Processing Time | Time per message | Above SLA |

### Dashboard Example
```
Queue Health:
+--------------------+
| Messages in Queue  | 1,234
| Processing Rate    | 500/sec
| Error Rate         | 0.1%
| Avg Processing Time| 45ms
| Consumer Count     | 5
| DLQ Size           | 12
+--------------------+
```

---

## Related Topics
- [[Microservices]] - Async service communication
- [[Scalability]] - Scaling with queues
- [[Distributed Systems]] - Distributed messaging

---

## Tags
#message-queues #rabbitmq #kafka #async #system-design #event-driven
