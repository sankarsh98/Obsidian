# Redis

> *In-memory data store*

---

## Why Redis?

- **Speed** - Sub-millisecond latency
- **Versatile** - Cache, sessions, queues
- **Data structures** - Lists, sets, sorted sets
- **Pub/Sub** - Real-time messaging

---

## Common Use Cases

| Use Case | Data Type |
|----------|-----------|
| Caching | String |
| Sessions | Hash |
| Rate limiting | String + INCR |
| Leaderboards | Sorted Set |
| Message queue | List |
| Real-time | Pub/Sub |

---

## Basic Operations

### Strings

```redis
SET user:1:name "John"
GET user:1:name

# With expiry
SET session:abc123 "user_data" EX 3600

# Increment
INCR page:views
INCRBY page:views 10
```

### Hashes

```redis
HSET user:1 name "John" email "john@example.com"
HGET user:1 name
HGETALL user:1
```

### Lists

```redis
LPUSH queue:jobs "job1"
RPUSH queue:jobs "job2"
LPOP queue:jobs
LRANGE queue:jobs 0 -1
```

### Sets

```redis
SADD tags:post:1 "python" "backend" "api"
SMEMBERS tags:post:1
SISMEMBER tags:post:1 "python"
```

### Sorted Sets

```redis
ZADD leaderboard 100 "player1"
ZADD leaderboard 200 "player2"
ZRANGE leaderboard 0 -1 WITHSCORES
ZREVRANK leaderboard "player2"
```

---

## Caching Pattern

```python
import redis

r = redis.Redis()

def get_user(user_id):
    # Try cache first
    cached = r.get(f"user:{user_id}")
    if cached:
        return json.loads(cached)
    
    # Fetch from database
    user = db.query(f"SELECT * FROM users WHERE id = {user_id}")
    
    # Cache for 1 hour
    r.setex(f"user:{user_id}", 3600, json.dumps(user))
    return user
```

---

*Back to: [[Index|Databases Home]]*
