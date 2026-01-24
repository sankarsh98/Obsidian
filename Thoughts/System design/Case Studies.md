# Case Studies

> Real-world system design examples and interview questions.

---

## Back to [[System Design]]

---

## How to Approach System Design

### Framework (RESHADED)

```
R - Requirements: Clarify functional and non-functional
E - Estimation: Calculate scale (users, data, QPS)
S - Storage: Design data model and choose databases
H - High-level design: Draw main components
A - API design: Define interfaces
D - Detailed design: Deep dive into components
E - Evaluation: Discuss trade-offs
D - Distinctive features: Handle edge cases
```

---

## Case Study 1: URL Shortener (TinyURL)

### Requirements

**Functional:**
- Shorten a long URL to a short URL
- Redirect short URL to original URL
- Optional: Custom short URLs
- Optional: Expiration

**Non-Functional:**
- High availability
- Low latency redirection
- Short URLs should be unpredictable

### Estimation

```
Assumptions:
- 100M URLs created per month
- Read:Write ratio = 100:1
- URLs stored for 5 years

Calculations:
- Writes: 100M / (30 * 24 * 3600) = ~40 writes/sec
- Reads: 40 * 100 = 4,000 reads/sec
- Storage: 100M * 12 * 5 = 6 billion URLs
- Data size: 6B * 500 bytes = 3TB
```

### Short URL Generation

```
Option 1: Base62 Encoding
Characters: a-z, A-Z, 0-9 (62 chars)
6 chars = 62^6 = 56.8 billion combinations
7 chars = 62^7 = 3.5 trillion combinations

Option 2: Hash + Truncate
hash = MD5(longURL)
shortCode = hash[:7]
Handle collisions

Option 3: Counter + Base62
counter = getNextId()  # 12345
shortCode = base62Encode(counter)  # "dnh"
```

### High-Level Design

```
+--------+     +-------------+     +----------+
| Client | --> | API Gateway | --> | App      |
+--------+     +-------------+     | Servers  |
                                   +----------+
                                       |
                    +------------------+------------------+
                    |                  |                  |
               +--------+        +---------+        +--------+
               | Cache  |        | Database|        | Counter|
               | (Redis)|        |(NoSQL)  |        | Service|
               +--------+        +---------+        +--------+
```

### Database Schema

```sql
-- URL Table
CREATE TABLE urls (
  id BIGINT PRIMARY KEY,
  short_code VARCHAR(10) UNIQUE,
  original_url TEXT NOT NULL,
  created_at TIMESTAMP,
  expires_at TIMESTAMP,
  user_id BIGINT
);

-- Index for lookups
CREATE INDEX idx_short_code ON urls(short_code);
```

### API Design

```
POST /api/v1/shorten
Body: { "url": "https://example.com/very/long/url" }
Response: { "shortUrl": "https://tiny.url/abc123" }

GET /{shortCode}
Response: 301 Redirect to original URL
```

### Key Decisions

| Decision | Choice | Reasoning |
|----------|--------|-----------|
| Database | NoSQL (DynamoDB) | Simple key-value, high scale |
| Cache | Redis | Fast reads, high hit rate |
| Short Code | Base62 + Counter | Unique, no collisions |
| Redirect | 301 (Permanent) | Cacheable by browsers |

---

## Case Study 2: Twitter/X Timeline

### Requirements

**Functional:**
- Post tweets (140/280 chars)
- Follow/unfollow users
- Home timeline (tweets from followed users)
- User timeline (user's own tweets)

**Non-Functional:**
- Timeline loads in < 200ms
- Handle 500M DAU
- Handle celebrity accounts (millions of followers)

### Estimation

```
- DAU: 500M
- Avg tweets read per user: 100/day
- Avg tweets posted per user: 2/day
- Avg follows per user: 200

Timeline reads: 500M * 100 = 50B/day = 580K/sec
Tweet writes: 500M * 2 = 1B/day = 11.5K/sec
```

### Timeline Approaches

#### Approach 1: Pull Model (Fan-out on read)

```
When user opens timeline:
1. Get list of followed users
2. For each user, get recent tweets
3. Merge and sort by time

Pros: Simple, no extra storage
Cons: Slow (especially with many follows)
```

#### Approach 2: Push Model (Fan-out on write)

```
When user posts tweet:
1. Get list of followers
2. Write tweet to each follower's timeline cache

Pros: Fast timeline reads
Cons: Slow writes for celebrities, high storage
```

#### Approach 3: Hybrid

```
Regular users: Push model (fan-out on write)
Celebrities: Pull model (fetch on read and merge)

Threshold: > 1M followers = celebrity
```

### High-Level Design

```
                                    +--------------+
                                    | Tweet Service|
                                    +--------------+
                                          |
+--------+     +---------+     +-------------------+
| Client | --> | API GW  | --> | Timeline Service  |
+--------+     +---------+     +-------------------+
                                    |         |
                               +--------+ +--------+
                               | Cache  | | Graph  |
                               | (Redis)| | DB     |
                               +--------+ +--------+
```

### Data Models

```
Tweet:
{
  "id": "tweet_123",
  "userId": "user_456",
  "content": "Hello world!",
  "createdAt": "2024-01-15T10:30:00Z",
  "mediaUrls": ["..."],
  "likeCount": 100,
  "retweetCount": 50
}

Timeline Cache (Redis):
Key: timeline:{userId}
Value: List of tweet IDs (sorted by time)
```

### Feed Generation

```python
def get_timeline(user_id, limit=100):
    # 1. Try cache first
    cached = redis.get(f"timeline:{user_id}")
    if cached:
        return cached[:limit]
    
    # 2. Get followed users
    following = graph_db.get_following(user_id)
    
    # 3. Separate celebrities and regular users
    celebrities = [u for u in following if u.followers > 1M]
    regular = [u for u in following if u.followers <= 1M]
    
    # 4. Get precomputed timeline for regular follows
    timeline = redis.get(f"timeline:{user_id}")
    
    # 5. Fetch celebrity tweets and merge
    for celeb in celebrities:
        tweets = tweet_db.get_recent(celeb.id)
        timeline = merge(timeline, tweets)
    
    # 6. Sort and return
    return sort_by_time(timeline)[:limit]
```

---

## Case Study 3: Rate Limiter

### Requirements

- Limit requests per user/IP
- Support different limits for different APIs
- Distributed (works across multiple servers)
- Low latency overhead

### Algorithms

#### Token Bucket

```
Bucket:
- Capacity: 10 tokens
- Refill: 1 token/second

Request arrives:
1. If tokens > 0: Allow, tokens--
2. If tokens = 0: Reject

+----------+     Request     +----------+
|  Client  | --------------> |  Bucket  |
+----------+                 | [●●●●○○] |
                             +----------+
                                  |
                           Allow or Reject
```

#### Sliding Window Log

```
Keep timestamps of all requests in window:

Window: 1 minute
Limit: 100 requests

[10:00:05, 10:00:15, 10:00:30, ...]

New request at 10:01:05:
1. Remove entries before 10:00:05
2. Count remaining entries
3. If < 100: Allow, add timestamp
4. If >= 100: Reject
```

#### Sliding Window Counter

```
Combine fixed windows with weighted average:

Previous window (10:00-10:01): 80 requests
Current window (10:01-10:02): 40 requests
Current position: 10:01:30 (50% into current window)

Weighted count = 80 * 0.5 + 40 * 0.5 = 60 requests
```

### Distributed Rate Limiting

```
Option 1: Centralized (Redis)
+----------+     +-------+     +--------+
| Server 1 | --> | Redis | <-- | Server 2|
+----------+     +-------+     +--------+
                 (shared counter)

Option 2: Local + Sync
Each server has local counter
Periodically sync with central store
Less accurate but faster
```

### Implementation (Redis)

```python
def is_rate_limited(user_id, limit=100, window=60):
    key = f"rate:{user_id}:{current_minute()}"
    
    # Atomic increment
    current = redis.incr(key)
    
    # Set expiry on first request
    if current == 1:
        redis.expire(key, window)
    
    return current > limit
```

---

## Case Study 4: Chat System (WhatsApp)

### Requirements

- 1:1 messaging
- Group chats (up to 500 members)
- Online/offline status
- Message delivery receipts
- Media support

### High-Level Design

```
+--------+     +---------+     +----------------+
| Client | <-->| Gateway | <-->| Chat Service   |
+--------+     +---------+     +----------------+
   |                               |
   | WebSocket                     |
   |                          +---------+
   +------------------------->| Message |
                              | Queue   |
                              +---------+
                                   |
                              +---------+
                              | Storage |
                              +---------+
```

### Message Flow

```
1. User A sends message to User B
2. Message goes to Chat Service via WebSocket
3. Chat Service stores message in DB
4. Chat Service checks if B is online
5a. If online: Send via WebSocket
5b. If offline: Store for later, send push notification
6. When delivered: Update delivery status
7. When read: Update read status
```

### Data Models

```json
// Message
{
  "id": "msg_123",
  "conversationId": "conv_456",
  "senderId": "user_a",
  "content": "Hello!",
  "type": "text",
  "status": "delivered",  // sent, delivered, read
  "createdAt": "2024-01-15T10:30:00Z"
}

// Conversation
{
  "id": "conv_456",
  "type": "direct",  // or "group"
  "participants": ["user_a", "user_b"],
  "lastMessage": {...},
  "unreadCount": {"user_a": 0, "user_b": 2}
}
```

### WebSocket Connection Management

```
+------------------+
| Connection       |
| Manager          |
+------------------+
| userId: server1  |
| userId: server2  |
| ...              |
+------------------+

When sending to user:
1. Check which server has connection
2. Route message to that server
3. Server sends via WebSocket
```

---

## Case Study 5: Video Streaming (YouTube/Netflix)

### Requirements

**Functional:**
- Upload video
- Stream video
- View analytics
- Search videos

**Non-Functional:**
- High availability
- Low buffering/latency
- Support multiple resolutions/devices

### Video Processing Flow

```
1. Original Video -> Upload -> Storage (S3)
2. Transcoding Service picks up video
3. Convert to formats: 360p, 720p, 1080p, 4K
4. Convert to codecs: H.264, VP9, HEVC
5. Split into chunks (DASH/HLS)
6. Upload chunks to CDN
```

### Adaptive Bitrate Streaming (ABS)

```
Client Network: High Bandwidth
Server sends: 1080p chunks

Network drops...

Client Network: Low Bandwidth
Server sends: 480p chunks (seamless switch)
```

### High-Level Design

```
           +--------+     +-----------+     +---------+
User Up -->| API GW | --> | Upload Svc| --> | S3 Raw  |
           +--------+     +-----------+     +---------+
                                                 |
                                            +-----------+
                                            | Transcoder|
                                            +-----------+
                                                 |
           +--------+     +-----------+     +---------+
User View <| CDN    | <-- | Metadata  | <-- | S3 Proc |
           +--------+     | DB        |     +---------+
```

### Directed Acyclic Graph (DAG) Model

For transcoding pipeline:
```
         [Validate]
             |
      +------+------+
      |             |
 [Thumbnail]   [Audio Ext]
      |             |
      |        [Transcode]
      |        /    |    \
      |    [360p] [720p] [1080p]
      |       \     |     /
      +--------\----|----/
                \   |   /
               [Packager]
```

---

## Case Study 6: Distributed File Storage (Google Drive/Dropbox)

### Requirements

- Upload/Download files
- Sync across devices
- File versioning
- Share files

### Core Concept: Block Storage

Don't store whole files. Split into blocks (e.g., 4MB).

```
File A (10MB):
- Block 1 (4MB) -> Hash: abc...
- Block 2 (4MB) -> Hash: def...
- Block 3 (2MB) -> Hash: ghi...

If only Block 3 changes, only upload Block 3 (Delta Sync).
Deduplication: If User B uploads same file, just point to existing blocks.
```

### Data Model

```
File Metadata DB:
- file_id
- user_id
- path
- version
- block_list: [hash1, hash2, hash3]

Block Storage (S3):
- Key: hash1, Value: binary_data
```

### Sync Conflict Resolution

```
Device A: Updates file (v1 -> v2)
Device B: Updates file (v1 -> v2')

Server Strategy:
- Last Write Wins (simple, data loss risk)
- Create conflict copy (safe, like Dropbox)
- Operational Transformation (complex, for real-time docs)
```

### High-Level Design

```
+--------+     +---------+     +----------+     +----------+
| Client | --> | Block   | --> | Block    | --> | S3 /     |
| (Watch)|     | Server  |     | Storage  |     | Cold Store|
+--------+     +---------+     +----------+     +----------+
    |
    +--------> | Meta    | --> | Meta DB  |
               | Server  |     | (SQL)    |
               +---------+     +----------+
                    |
               +---------+
               | Notif   |
               | Service |
               +---------+
```

---

## Case Study 7: Ride Sharing (Uber/Lyft)

### Requirements

- Match rider with driver
- Real-time location tracking
- Price estimation
- Trip history

### Geo-Spatial Indexing

How to find "drivers near me"?

#### Option 1: Geohash
Encodes lat/long into string.
```
New York: dr5ru...
Shared prefix = nearby.
```

#### Option 2: QuadTree
Recursively divide map into 4 quadrants.
```
       Root
      / | \ \
    NW NE SW SE
   /...\
```

#### Option 3: Google S2
Maps sphere to 1D index (Hilbert curve).

### Location Updates

Drivers update location every 3 seconds.
High write volume!

```
Driver App --> Location Service (Redis + Kafka)
                    |
              +-----+-----+
              |           |
        [Trip Match]  [Analytics]
```

### Matching Service

```
1. Rider requests ride (Lat, Long)
2. Find available drivers in S2 cell (and neighbors)
3. Filter by status, vehicle type
4. Send request to Driver A
5. If reject/timeout, send to Driver B
```

### Data Consistency

State Machine for Trip:
```
REQUESTED -> MATCHING -> ACCEPTED -> ARRIVED -> IN_PROGRESS -> COMPLETED
                                                 \
                                                  -> CANCELLED
```

---

## Case Study 8: Web Crawler (Google Search)

### Requirements

- Crawl billions of web pages
- Parse and index content
- Respect robots.txt
- Handle duplicate content

### Components

#### 1. URL Frontier
Priority queue of URLs to crawl.
- Priority: PageRank, update frequency
- Politeness: Rate limit per domain

#### 2. HTML Downloader
Fetches pages. Uses DNS resolver cache.

#### 3. Content Parser
Validates HTML, extracts links.

#### 4. Duplicate Detector
Fingerprint content (Checksum/SimHash).
If hash exists, skip.

### BFS vs DFS
Web crawling uses **BFS** (Breadth-First Search) generally, to explore high-level pages first.

### High-Level Design

```
      +-----------+      +------------+
      | Seed URLs | ---> | URL        |
      +-----------+      | Frontier   |
                         +------------+
                               |
                               v
+---------+          +--------------+
| Storage | <------- | HTML         |
+---------+          | Downloader   |
                     +--------------+
                               |
                               v
                     +--------------+
                     | Content      |
                     | Parser       |
                     +--------------+
                               |
      +-------------+          v
      | Duplicate   | <--- [Extracted]
      | Eliminator  |      [Links    ]
      +-------------+
```

---

## Case Study 9: Typeahead Suggestion (Autocomplete)

### Requirements

- Fast response (< 100ms)
- Suggestions sorted by popularity
- Update with new trends

### Data Structure: Trie (Prefix Tree)

```
        (root)
        /    \
       b      c
      / \      \
     e   a      a
    /     \      \
   e(10)   t(5)   t(8)
  (bee)   (bat)  (cat)
```

### Optimization: Top-K Storage

Store top 5 popular searches at each node.

```
Node 'b': [best, bee, bat, ...]
Node 'be': [best, bee, ...]
```
Reduces traversal time. O(1) lookup.

### Architecture

```
+--------+    +-----------+    +---------+
| Client | -> | Web Svr   | -> | Trie    |
+--------+    | (Nginx)   |    | Cache   |
              +-----------+    +---------+
                                    ^
                                    | build
                             +-------------+
                             | Aggregator  |
                             +-------------+
                                    ^
                             +-------------+
                             | Search Logs |
                             +-------------+
```

### Updates

- **Real-time**: Complex, locks Trie.
- **Batch**: Rebuild Trie every hour/day (Simpler).

---

## Case Study 10: Unique ID Generator

### Requirements

- Unique IDs
- Sortable by time
- 64-bit numeric (fit in bigint)
- High throughput

### Approaches

#### 1. Multi-Master Replication
auto_increment with step.
DB1: 1, 4, 7...
DB2: 2, 5, 8...
DB3: 3, 6, 9...
**Cons:** Hard to scale, not strictly time-sorted.

#### 2. UUID
128-bit alphanumeric.
**Cons:** Too big, not sortable, bad for DB indexing.

#### 3. Twitter Snowflake (Standard Solution)

64-bit integer structure:
```
| 1 bit | 41 bits    | 5 bits | 5 bits | 12 bits |
| sign  | timestamp  | datacenter | machine | sequence|
```

- **Timestamp**: Milliseconds since epoch (gives ~69 years)
- **Datacenter ID**: 32 IDs
- **Machine ID**: 32 IDs per DC
- **Sequence**: 4096 IDs per millisecond per machine

**Pros:**
- Time sortable
- Distributed (no coordination needed)
- Numeric and compact

---

## Case Study 11: Notification System

### Requirements

- Send Push, Email, SMS
- Bulk sending
- Prioritization (OTP > Marketing)
- Rate limiting (Don't spam users)

### Components

1.  **Notification Service**: Validates and routes requests.
2.  **User Preferences**: Opt-in/out status.
3.  **Message Queues**: Buffer requests. Separate queues by priority.
4.  **Workers**: Call 3rd party APIs (SendGrid, Twilio, FCM).
5.  **Status Service**: Track sent/failed/read status.

### High-Level Design

```
+---------+     +-----------+
| Service | --> | Notif Svc |
+---------+     +-----------+
                      |
        +-------------+-------------+
        |             |             |
   +---------+   +---------+   +---------+
   | OTP Q   |   | Trans Q |   | Promo Q |
   | (High)  |   | (Med)   |   | (Low)   |
   +---------+   +---------+   +---------+
        |             |             |
   +---------+   +---------+   +---------+
   | Workers |   | Workers |   | Workers |
   +---------+   +---------+   +---------+
        |             |             |
   +---------+   +---------+   +---------+
   | Twilio  |   | FCM     |   | SendGrid|
   +---------+   +---------+   +---------+
```

### Reliability

- **Retry Queue**: Exponential backoff for failures.
- **Deduplication**: Check message ID before sending.

---

## Design Patterns Quick Reference

### Common Patterns

| Pattern | Use Case | Example |
|---------|----------|---------|
| Sharding | Scale writes | User ID % N shards |
| Replication | Scale reads | Read replicas |
| Caching | Reduce latency | Redis for hot data |
| CDN | Static content | Images, JS, CSS |
| Message Queue | Async processing | Order processing |
| Circuit Breaker | Fault tolerance | External API calls |
| Rate Limiting | Protect resources | API rate limits |
| Consistent Hashing | Distributed cache | DynamoDB, Cassandra |
| Bloom Filter | Existence check | Crawler, Cache miss |

### Estimation Cheat Sheet

```
Time:
1 day = 86,400 sec ≈ 100K sec
1 month = 2.5M sec
1 year = 30M sec

Storage:
1 char = 1 byte (ASCII)
1 char = 2-4 bytes (UTF-8)
UUID = 16 bytes
Timestamp = 8 bytes
Long/Double = 8 bytes

QPS:
Low: < 100 (single server)
Medium: 100-10K (few servers)
High: 10K-100K (distributed)
Very High: > 100K (highly distributed)
```

---

## Interview Tips

### Do's
- Ask clarifying questions
- State assumptions explicitly
- Start with high-level design
- Discuss trade-offs
- Consider edge cases
- Be open to feedback

### Don'ts
- Don't dive into details too early
- Don't ignore scale
- Don't forget about failures
- Don't be silent (think out loud)
- Don't be defensive about feedback

### Structure Your Answer

```
1. Requirements (5 min)
   - Clarify functional requirements
   - Clarify non-functional requirements
   - Identify constraints

2. High-Level Design (10 min)
   - Draw main components
   - Explain data flow
   - Justify choices

3. Deep Dive (20 min)
   - Database design
   - API design
   - Scaling strategies
   - Handle edge cases

4. Wrap Up (5 min)
   - Summarize design
   - Discuss trade-offs
   - Mention improvements
```

---

## Practice Problems

### Easy
- [ ] URL Shortener
- [ ] Pastebin
- [ ] Rate Limiter
- [ ] Key-Value Store
- [ ] Unique ID Generator

### Medium
- [ ] Twitter Timeline
- [ ] Instagram Feed
- [ ] Notification System
- [ ] Web Crawler
- [ ] Chat System
- [ ] Typeahead/Autocomplete

### Hard
- [ ] Distributed Message Queue
- [ ] Search Engine
- [ ] Video Streaming (YouTube)
- [ ] File Storage (Dropbox)
- [ ] Ride Sharing (Uber)
- [ ] Collaborative Editor (Google Docs)

---

## Related Topics
- [[Fundamentals]] - Design approach
- [[Scalability]] - Scaling strategies
- [[Databases]] - Data storage options
- [[Caching]] - Performance optimization
- [[Distributed Systems]] - Consistency and consensus

---

## Tags
#case-studies #interview #system-design #practice
