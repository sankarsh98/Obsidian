# Streaming Pipeline Project

> *Build a real-time data pipeline*

---

## Project Overview

Build a streaming pipeline that:
- **Ingests** events in real-time via Kafka
- **Processes** with Spark Streaming
- **Serves** to a live dashboard

---

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Events    │     │   Kafka     │     │   Spark     │
│ (Producers) │────▶│   Topics    │────▶│  Streaming  │
└─────────────┘     └─────────────┘     └──────┬──────┘
                                               │
                        ┌──────────────────────┴───────┐
                        ▼                              ▼
                 ┌─────────────┐               ┌─────────────┐
                 │   Redis     │               │  PostgreSQL │
                 │ (Real-time) │               │ (Aggregate) │
                 └──────┬──────┘               └─────────────┘
                        │
                 ┌──────▼──────┐
                 │  Dashboard  │
                 │  (Grafana)  │
                 └─────────────┘
```

---

## Tech Stack

- **Messaging**: Apache Kafka
- **Processing**: Spark Structured Streaming
- **Storage**: Redis (real-time), PostgreSQL
- **Visualization**: Grafana
- **Containers**: Docker

---

## Implementation Steps

### Step 1: Kafka Setup
```yaml
# docker-compose.yml
services:
  kafka:
    image: confluentinc/cp-kafka:latest
```

### Step 2: Producer
```python
producer.send('events', {'user_id': 123, 'action': 'click'})
```

### Step 3: Spark Consumer
```python
df = spark.readStream \
    .format("kafka") \
    .option("subscribe", "events") \
    .load()

query = df.writeStream \
    .outputMode("append") \
    .format("console") \
    .start()
```

### Step 4: Live Dashboard
Connect Grafana to Redis for real-time metrics.

---

*Back to: [[Index|Projects Home]]*
