# Caching

> Storing frequently accessed data in fast storage to reduce latency and database load.

---

## Back to [[System Design]]

---

## What is Caching?

Caching is the process of storing copies of data in a high-speed storage layer (cache) so that future requests for that data can be served faster than accessing the primary storage location.

```
Without Cache:                 With Cache:
Client --> Database (slow)     Client --> Cache (fast) --> Database (if miss)
         100ms                          1ms              100ms (only on miss)
```

---

## Cache Levels

```
+------------------+  Fastest, Smallest
|   L1/L2 Cache    |  (CPU level)
+------------------+
         |
+------------------+
|  Application     |  (In-memory: HashMap)
+------------------+
         |
+------------------+
|  Distributed     |  (Redis, Memcached)
+------------------+
         |
+------------------+
|      CDN         |  (Edge locations)
+------------------+
         |
+------------------+  Slowest, Largest
|    Database      |
+------------------+
```

---

## Caching Strategies

### 1. Cache-Aside (Lazy Loading)

Application manages the cache directly.

```
Read:
+--------+    1. Check cache    +-------+
| Client | ------------------>  | Cache |
+--------+                      +-------+
    |                               |
    |  3. Return data               | 2. Cache miss
    |<------------------------------|
    |                               v
    |                          +----+----+
    |                          |   DB    |
    |                          +---------+
    |                               |
    |  4. Store in cache            |
    |------------------------------>|
```

```python
def get_user(user_id):
    # 1. Check cache
    user = cache.get(f"user:{user_id}")
    if user:
        return user  # Cache hit
    
    # 2. Cache miss - get from DB
    user = db.query(f"SELECT * FROM users WHERE id = {user_id}")
    
    # 3. Store in cache
    cache.set(f"user:{user_id}", user, ttl=3600)
    
    return user
```

**Pros:** Only caches what's needed, resilient to cache failures
**Cons:** Cache miss penalty, potential stale data

### 2. Read-Through

Cache sits between application and database.

```
+--------+     +-------+     +----+
| Client | --> | Cache | --> | DB |
+--------+     +-------+     +----+
                   |
          (Cache handles DB reads)
```

**Pros:** Simpler application code, consistent caching
**Cons:** Cache library must support, cold start issues

### 3. Write-Through

Data written to cache and database simultaneously.

```
Write:
+--------+     +-------+     +----+
| Client | --> | Cache | --> | DB |
+--------+     +-------+     +----+
                   |
          (Writes go to both)
```

```python
def update_user(user_id, data):
    # Write to DB
    db.update(f"UPDATE users SET ... WHERE id = {user_id}")
    
    # Write to cache
    cache.set(f"user:{user_id}", data, ttl=3600)
```

**Pros:** Cache always consistent, no stale data
**Cons:** Higher write latency, writes even for uncached data

### 4. Write-Behind (Write-Back)

Write to cache immediately, sync to database asynchronously.

```
+--------+     +-------+     +-------+     +----+
| Client | --> | Cache | --> | Queue | --> | DB |
+--------+     +-------+     +-------+     +----+
                   |              |
            (Immediate)     (Async batch)
```

**Pros:** Low write latency, batched DB writes
**Cons:** Risk of data loss, complexity

### 5. Refresh-Ahead

Proactively refresh cache before expiration.

```
TTL: 60 seconds
Refresh at: 50 seconds (before expiry)

Timeline:
0s -------- 50s -------- 60s
   Cache     Refresh      Would expire
   valid     triggered    (but already refreshed)
```

**Pros:** No cache miss latency, always fresh
**Cons:** May refresh unused data, complexity

---

## Caching Strategies Comparison

| Strategy | Best For | Consistency | Complexity |
|----------|----------|-------------|------------|
| Cache-Aside | Read-heavy, tolerates stale | Eventual | Low |
| Read-Through | Read-heavy, simple code | Eventual | Medium |
| Write-Through | Read-heavy, needs consistency | Strong | Medium |
| Write-Behind | Write-heavy | Eventual | High |
| Refresh-Ahead | Predictable access patterns | Strong | High |

---

## Cache Eviction Policies

### LRU (Least Recently Used)
```
Access order: A, B, C, D, A, E (cache size: 4)

[A, B, C, D] --> Access A --> [B, C, D, A]
[B, C, D, A] --> Add E   --> [C, D, A, E]  (B evicted)
```
**Best for:** General purpose, recency matters

### LFU (Least Frequently Used)
```
Frequency count:
A: 5, B: 2, C: 8, D: 1

Add E (cache full) --> D evicted (lowest frequency)
```
**Best for:** When popularity matters more than recency

### FIFO (First In, First Out)
```
[A, B, C, D] --> Add E --> [B, C, D, E]  (A evicted)
```
**Best for:** Simple, when access patterns are uniform

### TTL (Time To Live)
```
Key: user:123
TTL: 3600 seconds
Created: 10:00:00
Expires: 11:00:00
```
**Best for:** Time-sensitive data

---

## Cache Invalidation

> "There are only two hard things in Computer Science: cache invalidation and naming things." - Phil Karlton

### Strategies

#### 1. TTL-Based
```
cache.set("user:123", data, ttl=3600)  # Expires in 1 hour
```

#### 2. Event-Based
```python
def update_user(user_id, data):
    db.update(user_id, data)
    cache.delete(f"user:{user_id}")  # Invalidate on update
```

#### 3. Version-Based
```
Key: user:123:v5
On update: user:123:v6 (new version, old expires)
```

#### 4. Pub/Sub
```
+----------+     Publish      +-------+
| Service  | --------------> | Pub/  |
| (Update) |                 | Sub   |
+----------+                 +-------+
                                |
                   +------------+------------+
                   |            |            |
              +--------+   +--------+   +--------+
              |Cache 1 |   |Cache 2 |   |Cache 3 |
              +--------+   +--------+   +--------+
```

---

## Distributed Caching

### Redis vs Memcached

| Feature | Redis | Memcached |
|---------|-------|-----------|
| Data Structures | Rich (lists, sets, hashes) | Simple (strings) |
| Persistence | Yes | No |
| Replication | Yes | No |
| Clustering | Yes | Client-side |
| Pub/Sub | Yes | No |
| Lua Scripting | Yes | No |
| Memory Efficiency | Good | Better |

### Redis Cluster Architecture

```
+-------------+     +-------------+     +-------------+
|   Master 1  |     |   Master 2  |     |   Master 3  |
| (slots 0-5k)|     |(slots 5k-10k)|    |(slots 10k-16k)|
+-------------+     +-------------+     +-------------+
       |                   |                   |
+-------------+     +-------------+     +-------------+
|   Slave 1   |     |   Slave 2   |     |   Slave 3   |
+-------------+     +-------------+     +-------------+
```

---

## CDN (Content Delivery Network)

### How CDN Works

```
                    Origin Server (NYC)
                           |
         +-----------------+-----------------+
         |                 |                 |
    Edge (LA)         Edge (London)     Edge (Tokyo)
         |                 |                 |
    User (CA)         User (UK)        User (Japan)
```

### CDN Use Cases

| Content Type | TTL | Example |
|--------------|-----|---------|
| Static assets | Long (1 year) | JS, CSS, images |
| API responses | Short (minutes) | User data |
| HTML pages | Medium (hours) | Blog posts |
| Video/Media | Long (days) | Streaming content |

### Cache-Control Headers

```http
# Cache for 1 year (immutable assets)
Cache-Control: public, max-age=31536000, immutable

# Cache for 1 hour, revalidate after
Cache-Control: public, max-age=3600, must-revalidate

# No caching
Cache-Control: no-store, no-cache

# Private (only browser, not CDN)
Cache-Control: private, max-age=3600
```

---

## Caching Patterns

### Cache Stampede Prevention

When cache expires, many requests hit the database simultaneously.

```
Solutions:

1. Locking
   - First request acquires lock
   - Others wait or return stale data

2. Probabilistic Early Expiration
   - Randomly refresh before TTL

3. Background Refresh
   - Refresh in background, serve stale
```

### Hot Key Problem

Single key receiving extremely high traffic.

```
Solutions:

1. Local Caching
   - Cache hot keys in application memory

2. Key Replication
   - user:123:r1, user:123:r2, user:123:r3
   - Randomly select replica

3. Rate Limiting
   - Limit requests per key
```

---

## Cache Metrics

| Metric | Formula | Target |
|--------|---------|--------|
| Hit Rate | Hits / (Hits + Misses) | > 90% |
| Miss Rate | Misses / (Hits + Misses) | < 10% |
| Latency | Response time | < 1ms |
| Memory Usage | Used / Total | < 80% |
| Eviction Rate | Evictions / Time | Low |

---

## Best Practices

1. **Cache what's expensive** - DB queries, API calls, computations
2. **Set appropriate TTLs** - Balance freshness vs performance
3. **Monitor hit rates** - Low hit rate = wasted resources
4. **Plan for cache failures** - System should work without cache
5. **Use cache warming** - Pre-populate cache after deploy
6. **Avoid caching sensitive data** - Or encrypt it

---

## Related Topics
- [[Databases]] - Primary data storage
- [[Scalability]] - Caching for scale
- [[Load Balancing]] - Distributing cache requests

---

## Tags
#caching #redis #memcached #cdn #performance #system-design
