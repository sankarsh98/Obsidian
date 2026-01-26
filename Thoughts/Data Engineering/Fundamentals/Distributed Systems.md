# Distributed Systems

> *Understanding scale and reliability*

---

## Why Learn Distributed Systems?

Modern data engineering involves:
- Processing terabytes/petabytes of data
- Systems that never go down
- Global data distribution
- Real-time processing at scale

---

## Core Concepts

### CAP Theorem

You can only have **2 of 3**:

| Property | Description |
|----------|-------------|
| **Consistency** | All nodes see the same data at the same time |
| **Availability** | Every request gets a response |
| **Partition Tolerance** | System works despite network failures |

**Trade-offs:**
- **CP Systems**: Sacrifice availability (HBase, MongoDB with strong consistency)
- **AP Systems**: Sacrifice consistency (Cassandra, DynamoDB)
- **CA Systems**: Only possible without partitions (traditional RDBMS)

---

### Partitioning (Sharding)

Splitting data across machines.

**Types:**
- **Range Partitioning**: By date, ID ranges
- **Hash Partitioning**: Hash of key determines partition
- **List Partitioning**: Explicit assignment

```
┌─────────────────────────────────────────┐
│               Original Data             │
└───────────────────┬─────────────────────┘
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
   ┌─────────┐ ┌─────────┐ ┌─────────┐
   │Partition│ │Partition│ │Partition│
   │    1    │ │    2    │ │    3    │
   │ (A-H)   │ │ (I-P)   │ │ (Q-Z)   │
   └─────────┘ └─────────┘ └─────────┘
```

---

### Replication

Copying data for reliability and performance.

**Strategies:**
- **Leader-Follower**: One primary, many replicas
- **Multi-Leader**: Multiple primaries
- **Leaderless**: Any node can accept writes

```
        ┌─────────┐
        │ Leader  │
        │ (Write) │
        └────┬────┘
             │
    ┌────────┼────────┐
    ▼        ▼        ▼
┌───────┐ ┌───────┐ ┌───────┐
│Replica│ │Replica│ │Replica│
│ (Read)│ │ (Read)│ │ (Read)│
└───────┘ └───────┘ └───────┘
```

---

### Consistency Models

| Model | Description | Example |
|-------|-------------|---------|
| **Strong** | Reads always return latest write | Traditional RDBMS |
| **Eventual** | Eventually all reads return latest | DynamoDB, Cassandra |
| **Causal** | Maintains cause-effect order | Some NoSQL |

---

## MapReduce Paradigm

Foundation of distributed processing.

```
Input → Map → Shuffle → Reduce → Output
```

**Example: Word Count**
```python
# Map Phase
def map(document):
    for word in document.split():
        emit(word, 1)

# Reduce Phase  
def reduce(word, counts):
    emit(word, sum(counts))
```

```
Document: "hello world hello"

Map Output:
  ("hello", 1)
  ("world", 1)
  ("hello", 1)

Shuffle: Group by key
  "hello" → [1, 1]
  "world" → [1]

Reduce Output:
  ("hello", 2)
  ("world", 1)
```

---

## Key Patterns

### Batch Processing
- Process all data periodically
- Higher latency, higher throughput
- Tools: Spark, MapReduce

### Stream Processing
- Process data as it arrives
- Lower latency, continuous
- Tools: Kafka Streams, Flink, Spark Streaming

### Lambda Architecture
```
                    ┌──────────────┐
                    │ Batch Layer  │──→ Batch Views
                    └──────┬───────┘
Data ──┤                   │
       │                   ▼
       │            ┌──────────────┐
       └───────────▶│ Speed Layer  │──→ Real-time Views
                    └──────────────┘
                           │
                    ┌──────▼───────┐
                    │ Serving Layer│──→ Queries
                    └──────────────┘
```

### Kappa Architecture
Simplified - streaming only:
```
Data ──→ Stream Processing ──→ Serving Layer ──→ Queries
```

---

## Common Challenges

| Challenge | Solution |
|-----------|----------|
| **Data Skew** | Salting keys, repartitioning |
| **Hot Spots** | Better partitioning strategy |
| **Network Failures** | Retries, idempotency |
| **Stale Reads** | Consistency requirements |
| **Coordination** | Consensus algorithms (Raft, Paxos) |

---

## Tools by Category

| Purpose | Tools |
|---------|-------|
| **Distributed Storage** | HDFS, S3, GCS |
| **Distributed Compute** | Spark, Flink, MapReduce |
| **Distributed Database** | Cassandra, HBase, CockroachDB |
| **Message Queue** | Kafka, Pulsar, RabbitMQ |
| **Coordination** | ZooKeeper, etcd |

---

## Reading List

- *Designing Data-Intensive Applications* - Martin Kleppmann
- [MIT 6.824 Distributed Systems](https://pdos.csail.mit.edu/6.824/)

---

*Back to: [[Index|Fundamentals Home]]*
