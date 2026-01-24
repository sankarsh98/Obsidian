# Distributed Systems

> Understanding the challenges and patterns of building systems across multiple machines.

---

## Back to [[System Design]]

---

## What are Distributed Systems?

A distributed system is a collection of independent computers that appear to users as a single coherent system. The computers communicate and coordinate their actions by passing messages.

```
+--------+     +--------+     +--------+
| Node 1 |<--->| Node 2 |<--->| Node 3 |
+--------+     +--------+     +--------+
     ^              ^              ^
     |              |              |
     v              v              v
+------------------------------------------+
|            Network (unreliable)          |
+------------------------------------------+
```

---

## CAP Theorem

> In a distributed system, you can only guarantee 2 out of 3 properties.

```
        Consistency
            /\
           /  \
          /    \
         /      \
        /   CA   \
       /          \
      /____________\
Availability ---- Partition Tolerance
       AP              CP
```

### The Three Properties

| Property | Definition |
|----------|------------|
| **Consistency (C)** | Every read receives the most recent write |
| **Availability (A)** | Every request receives a response |
| **Partition Tolerance (P)** | System operates despite network failures |

### Why Only 2?

Network partitions **will** happen. So you must choose between:

```
Network Partition Occurs:

CP Choice (Consistency + Partition Tolerance):
Node A: "Write x=5"
Node B: [partition] [BLOCKED - refuses to respond]
Result: Consistent but unavailable

AP Choice (Availability + Partition Tolerance):
Node A: "Write x=5" (x=5)
Node B: [partition] "Read x" --> returns x=3 (stale)
Result: Available but inconsistent
```

### Real-World Examples

| System | Choice | Reasoning |
|--------|--------|-----------|
| Banks (transactions) | CP | Correctness over availability |
| Social Media (posts) | AP | Availability over consistency |
| E-commerce (inventory) | CP | Can't oversell |
| DNS | AP | High availability needed |

---

## PACELC Theorem

> Extension of CAP: what happens when there's NO partition?

```
If Partition:
  Choose between Consistency and Availability (CAP)
Else (no partition):
  Choose between Latency and Consistency

PACELC = PAC | ELC
```

| System | P: A or C | E: L or C |
|--------|-----------|-----------|
| DynamoDB | PA | EL |
| Cassandra | PA | EL |
| MongoDB | PC | EC |
| PostgreSQL | PC | EC |

---

## Consistency Models

### Strong Consistency
```
Client A: Write x=5 (commits at T1)
Client B: Read x (at T2, T2 > T1) --> Always returns 5

All nodes see the same data at the same time.
```

### Eventual Consistency
```
Client A: Write x=5 (to Node 1)
Client B: Read x (from Node 2) --> Might return old value
...wait...
Eventually: All nodes return x=5
```

### Causal Consistency
```
Client A: Write x=5, then Write y=10
Client B: If sees y=10, guaranteed to see x=5

Causally related operations are seen in order.
```

### Read-Your-Writes Consistency
```
Client A: Write x=5
Client A: Read x --> Guaranteed to see 5

A client always sees its own writes.
```

### Consistency Spectrum

```
Weak                                          Strong
  |                                              |
  v                                              v
Eventual --> Causal --> Read-Your-Writes --> Linearizable
  |                                              |
Fast,                                      Slow,
Available                                  Consistent
```

---

## Consensus Algorithms

### The Problem

> How do distributed nodes agree on a value?

```
Scenario: Electing a leader

Node A: "I vote for A"
Node B: "I vote for B"
Node C: "I vote for A"

How to safely agree A is leader?
```

### Paxos

Classic consensus algorithm (complex).

```
Phases:
1. Prepare: Proposer sends prepare(n)
2. Promise: Acceptors promise to accept proposal n
3. Accept: Proposer sends accept(n, value)
4. Accepted: Majority accepts --> Consensus reached
```

### Raft

Simpler consensus algorithm.

```
States: Leader, Follower, Candidate

Leader Election:
1. Follower times out (no heartbeat)
2. Becomes Candidate, requests votes
3. Majority votes --> Becomes Leader
4. Leader sends heartbeats

Log Replication:
1. Client sends command to Leader
2. Leader appends to log
3. Leader replicates to Followers
4. Once majority confirms --> Commit
```

```
+----------+     Heartbeats    +----------+
|  Leader  |------------------>| Follower |
|          |------------------>| Follower |
|          |------------------>| Follower |
+----------+                   +----------+
     ^
     |
Client writes go to Leader only
```

---

## Distributed Transactions

### Two-Phase Commit (2PC)

```
Phase 1: Prepare
Coordinator: "Can everyone commit?"
    |
    +---> Node A: "Yes, I'm prepared"
    +---> Node B: "Yes, I'm prepared"
    +---> Node C: "Yes, I'm prepared"

Phase 2: Commit
Coordinator: "Everyone commit!"
    |
    +---> Node A: [commits]
    +---> Node B: [commits]
    +---> Node C: [commits]
```

**Pros:** Strong consistency
**Cons:** Blocking, coordinator is SPOF

### Three-Phase Commit (3PC)

Adds pre-commit phase to reduce blocking.

```
1. CanCommit: Coordinator asks if nodes can commit
2. PreCommit: Nodes prepare but don't commit
3. DoCommit: Nodes commit

Non-blocking (nodes can timeout and recover)
```

### Saga Pattern

See [[Microservices#Saga Pattern]]

---

## Clock Synchronization

### The Problem

```
Node A (clock): 10:00:05
Node B (clock): 10:00:03
Event happens on both at "same time"

Which happened first?
```

### Lamport Clocks

Logical clocks for ordering events.

```
Node A: counter=1 --> Event --> counter=2
        Send message (counter=2)
                |
                v
Node B: counter=1 --> Receive (max(1,2)+1=3) --> counter=3

If A happened before B: clock(A) < clock(B)
But not vice versa!
```

### Vector Clocks

Track causality across nodes.

```
Node A: [A:1, B:0]
Node B: [A:0, B:1]

A sends to B:
Node B: [A:1, B:2]

Can detect: causality and concurrency
```

### Real-World Solutions

| Solution | Accuracy | Complexity |
|----------|----------|------------|
| NTP | ~10-100ms | Low |
| GPS | ~nanoseconds | Medium |
| Atomic clocks | ~nanoseconds | High |
| Google TrueTime | ~7ms | High |

---

## Failure Modes

### Types of Failures

```
1. Crash Failure
   Node stops working entirely
   Easy to detect (timeouts)

2. Omission Failure
   Node fails to send/receive messages
   Hard to distinguish from slowness

3. Byzantine Failure
   Node behaves arbitrarily (malicious or buggy)
   Hardest to handle
```

### Failure Detection

```
Heartbeat-based:
Node A --heartbeat--> Monitor
        --heartbeat-->
        --heartbeat-->
        [silence]     --> Suspect failure
        [more silence]--> Declare dead
```

### Handling Failures

```
Strategy 1: Retry
Request --> Failed --> Wait --> Retry --> Success

Strategy 2: Fallback
Request --> Failed --> Use cached/default value

Strategy 3: Circuit Breaker
Request --> Many failures --> Open circuit --> Fast fail
```

---

## Data Replication

### Single Leader

```
Write --> Leader --> Follower 1
                --> Follower 2
Read <-- Leader or Followers
```

### Multi-Leader

```
Write --> Leader A <--> Leader B <-- Write
             |              |
          Followers      Followers
          
Conflict resolution needed
```

### Leaderless (Quorum)

```
Write --> Node A (W)
      --> Node B (W)
      --> Node C (success with 2/3)
      
Read  --> Node A (R)
      --> Node B (R)
      --> Node C (success with 2/3)
      
W + R > N ensures consistency
```

### Quorum Formula

```
N = Total nodes
W = Write quorum
R = Read quorum

Strong consistency: W + R > N
Example: N=3, W=2, R=2 (2+2 > 3)
```

---

## Distributed Patterns

### Leader Election

```
Using distributed lock (e.g., etcd, ZooKeeper):

1. Nodes try to acquire lock
2. Winner becomes leader
3. Leader renews lock periodically
4. If leader fails, lock expires
5. New election begins
```

### Consistent Hashing

See [[Databases#Consistent Hashing]]

### Gossip Protocol

```
Epidemic information spread:

Node A knows: {A: healthy, B: healthy}
Node A tells random Node C
Node C knows: {A: healthy, B: healthy, C: healthy}
Node C tells random Node B
...eventually all nodes know everything

Used by: Cassandra, DynamoDB
```

---

## Distributed System Fallacies

> The 8 Fallacies of Distributed Computing

```
1. The network is reliable
2. Latency is zero
3. Bandwidth is infinite
4. The network is secure
5. Topology doesn't change
6. There is one administrator
7. Transport cost is zero
8. The network is homogeneous

Reality: All these assumptions are FALSE
```

---

## Design Principles

### 1. Design for Failure
```
Assume: Any component can fail at any time
Plan: Retries, fallbacks, graceful degradation
```

### 2. Embrace Eventual Consistency
```
When possible: Accept eventual consistency
Benefit: Higher availability, lower latency
```

### 3. Idempotency
```
Retry-safe operations:
DELETE user/123 (idempotent - same result)
x = 5 (idempotent)

Not idempotent:
x = x + 1 (different result each time)
```

### 4. Bulkhead Pattern
```
Isolate failures:
+----------+  +----------+  +----------+
| Service A|  | Service B|  | Service C|
+----------+  +----------+  +----------+
     |              |              |
Separate thread pools / connections

A's failure doesn't exhaust B's resources
```

---

## Tools and Technologies

| Category | Tools |
|----------|-------|
| Coordination | ZooKeeper, etcd, Consul |
| Messaging | Kafka, RabbitMQ, Pulsar |
| Databases | Cassandra, CockroachDB, Spanner |
| Service Mesh | Istio, Linkerd, Envoy |
| Observability | Jaeger, Prometheus, Grafana |

---

## Related Topics
- [[Databases]] - Distributed database patterns
- [[Microservices]] - Distributed architecture
- [[Message Queues]] - Distributed messaging

---

## Tags
#distributed-systems #cap-theorem #consensus #replication #system-design
