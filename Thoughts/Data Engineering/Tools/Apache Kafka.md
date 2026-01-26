# Apache Kafka

> *Distributed event streaming platform*

---

## What is Kafka?

Apache Kafka is a **distributed streaming platform** for building real-time data pipelines and streaming applications.

---

## Core Concepts

### Architecture

```
┌──────────────┐     ┌─────────────────────────────────┐     ┌──────────────┐
│              │     │         Kafka Cluster           │     │              │
│  Producers   │────▶│  ┌─────────┐  ┌─────────┐      │────▶│  Consumers   │
│              │     │  │ Broker 1│  │ Broker 2│      │     │              │
└──────────────┘     │  └─────────┘  └─────────┘      │     └──────────────┘
                     │         │           │          │
                     │         ▼           ▼          │
                     │  ┌─────────────────────────┐   │
                     │  │       Topics            │   │
                     │  │  ┌─────┐ ┌─────┐       │   │
                     │  │  │Part0│ │Part1│ ...   │   │
                     │  │  └─────┘ └─────┘       │   │
                     │  └─────────────────────────┘   │
                     └────────────────────────────────┘
```

### Key Terms

| Term | Description |
|------|-------------|
| **Topic** | Category/feed name for messages |
| **Partition** | Ordered, immutable sequence of messages |
| **Offset** | Unique ID for each message in a partition |
| **Producer** | Publishes messages to topics |
| **Consumer** | Reads messages from topics |
| **Consumer Group** | Set of consumers sharing workload |
| **Broker** | Kafka server that stores data |

---

## Basic Operations

### Producer

```python
from kafka import KafkaProducer
import json

producer = KafkaProducer(
    bootstrap_servers=['localhost:9092'],
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

# Send message
producer.send('my-topic', {'user_id': 123, 'action': 'click'})
producer.flush()  # Wait for all messages to be sent
```

### Consumer

```python
from kafka import KafkaConsumer
import json

consumer = KafkaConsumer(
    'my-topic',
    bootstrap_servers=['localhost:9092'],
    group_id='my-group',
    auto_offset_reset='earliest',
    value_deserializer=lambda m: json.loads(m.decode('utf-8'))
)

for message in consumer:
    print(f"Received: {message.value}")
```

---

## Key Features

### Partitioning

```python
# Specify partition key for ordering
producer.send('orders', 
              key=str(user_id).encode(),  # All user's orders go to same partition
              value={'order_id': 123})
```

### Consumer Groups

```
Topic: orders (3 partitions)
          │
    ┌─────┼─────┐
    ▼     ▼     ▼
  Part0 Part1 Part2
    │     │     │
    ▼     ▼     ▼
┌─────────────────┐
│  Consumer Group │
│ ┌────┐ ┌────┐  │
│ │ C1 │ │ C2 │  │  ← Work is distributed
│ └────┘ └────┘  │
└─────────────────┘
```

### Kafka Connect

Pre-built connectors for common sources/sinks:
- Databases (PostgreSQL, MySQL)
- Cloud storage (S3, GCS)
- Elasticsearch, MongoDB

---

## Common Patterns

### Log Compaction
Keep only latest value per key.

### Exactly-Once Semantics
Guaranteed no duplicates or data loss.

### Event Sourcing
Store all events, derive current state.

---

## Docker Setup

```yaml
# docker-compose.yml
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
  
  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
```

---

*Next: [[Apache Airflow|Airflow →]]*

*Back to: [[Index|Tools Home]]*
