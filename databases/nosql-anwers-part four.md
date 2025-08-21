# 📦 NoSQL Deep Dive (Part 4)  
**MongoDB Sharding, Replication, and Redis**

This document covers **advanced NoSQL concepts** — essential for **scalability**, **availability**, and **performance optimization**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 16. What is sharding in MongoDB?

> **Sharding** is MongoDB’s method of **horizontal scaling** — splitting data across multiple servers (shards) to handle **large datasets** and **high throughput**.

It’s how MongoDB scales **beyond a single machine**.

---

### 🔹 How Sharding Works

```
[Application] → [Query Router (mongos)] → [Shard 1 | Shard 2 | Shard 3]
```

- **Shard**: A server (or replica set) holding a subset of data
- **mongos**: Query router — routes queries to correct shard
- **Config Servers**: Store metadata (e.g., "shard key X → shard 1")

---

### 🔹 Shard Key

The **shard key** determines how data is distributed.

```javascript
// Example: Shard users by userId
sh.shardCollection("mydb.users", { "userId": 1 })
```

✅ Good shard key:
- High cardinality (many unique values)
- Even distribution (no "hot" shards)
- Used in queries (so mongos can route efficiently)

❌ Bad shard key: `status` (low cardinality — "active", "inactive")

---

### 🔹 Sharding Strategies

| Strategy | How | Use Case |
|--------|-----|--------|
| **Range-Based** | Documents with close shard key values on same shard | Time-series with `timestamp` |
| **Hash-Based** | Hash of shard key → even distribution | `userId`, `email` |
| **Zones (Tag-Aware)** | Route data to specific shards (e.g., by region) | GDPR compliance (EU data in EU) |

---

### 🔹 Benefits

| Benefit | Explanation |
|--------|-------------|
| ✅ **Horizontal Scaling** | Add more shards to scale out |
| ✅ **High Throughput** | Distribute read/write load |
| ✅ **Large Datasets** | Handle terabytes of data |
| ✅ **Parallel Processing** | Aggregation can run on each shard |

---

### 📌 When to Shard

| Scenario | Why |
|--------|-----|
| ✅ Data > 1TB | Too big for single server |
| ✅ Write throughput > 1K ops/sec | CPU/disk bottleneck |
| ✅ High availability across regions | Geo-distributed shards |

---

### 📌 Interview Answer

> _"Sharding splits data across multiple servers for horizontal scaling. I choose a good shard key — high cardinality, even distribution. MongoDB uses mongos as a router. I use sharding when data or traffic outgrows a single replica set. It’s essential for large-scale applications."_  

---

## 17. What is replication in MongoDB? Why is it important?

> **Replication** in MongoDB is the process of **synchronizing data across multiple servers** (a **replica set**) to ensure **high availability** and **data redundancy**.

---

### 🔹 Replica Set Architecture

```
[Primary] ←→ [Secondary 1]
   ↑             ↑
[Secondary 2] ←→ [Arbiter (optional)]
```

- **Primary**: Handles all writes and reads (by default)
- **Secondaries**: Replicate data from primary, handle read queries
- **Arbiter**: Votes in elections (no data)

---

### 🔹 How It Works

1. **Write to Primary** → written to **oplog** (operation log)
2. **Secondaries** replicate oplog entries asynchronously
3. If primary fails → **election** → one secondary becomes primary
4. Old primary rejoins as secondary when back

✅ Automatic failover in **seconds**

---

### 🔹 Read Preferences

Clients can choose where to read:

| Mode | Use Case |
|------|--------|
| `primary` | Strong consistency (default) |
| `primaryPreferred` | Prefer primary, fall back to secondary |
| `secondary` | Offload reads (analytics) |
| `nearest` | Lowest network latency |

---

### 🔹 Why Replication is Important

| Benefit | Explanation |
|--------|-------------|
| ✅ **High Availability** | No single point of failure |
| ✅ **Data Redundancy** | Protects against hardware failure |
| ✅ **Disaster Recovery** | Secondaries in different data centers |
| ✅ **Read Scaling** | Distribute read load |
| ✅ **Backup** | Run backups on secondaries (no impact on primary) |

---

### 📌 Interview Answer

> _"Replication in MongoDB uses a replica set — one primary, multiple secondaries. It ensures high availability and data redundancy. If the primary fails, a secondary is automatically elected. I use it in production for fault tolerance. I can also offload reads to secondaries for performance."_  

---

## 18. What is Redis? What type of NoSQL database is it?

> **Redis** (Remote Dictionary Server) is an **in-memory key-value store** — often called a **data structure server**.

It’s **blazing fast** because data lives in **RAM**, not on disk.

---

### 🔹 Type: Key-Value Store

| Feature | Description |
|--------|-------------|
| **Model** | Key → Value (string, JSON, binary, or rich data structures) |
| **Speed** | Sub-millisecond reads/writes |
| **Persistence** | Optional (RDB snapshots, AOF logs) |
| **Use Case** | Caching, sessions, real-time analytics, queues |

---

### 🔹 Why "Data Structure Server"?

Redis doesn’t just store strings — it supports **rich data types**:
- Strings
- Hashes
- Lists
- Sets
- Sorted Sets
- Bitmaps
- Streams

✅ You can **operate on the data server-side** — no need to read, modify, write.

---

### 🔹 Example: User Session
```bash
SET session:abc123:user_id "123"
EXPIRE session:abc123:user_id 3600  # Auto-delete in 1 hour
```

✅ Fast session lookup, automatic cleanup.

---

### 📌 Interview Answer

> _"Redis is an in-memory key-value store, but more accurately a 'data structure server' because it supports lists, sets, hashes, and more. I use it for caching, session storage, and real-time features. It's fast because everything is in RAM, and I can set TTLs for auto-expiry."_  

---

## 19. When would you use Redis?

| Use Case | Why Redis? |
|--------|-----------|
| ✅ **Caching** | Speed up database queries (cache-aside pattern) |
| ✅ **Session Storage** | Fast, ephemeral user sessions |
| ✅ **Rate Limiting** | Track requests per IP/user with `INCR` and `EXPIRE` |
| ✅ **Leaderboards** | Sorted Sets for real-time rankings |
| ✅ **Pub/Sub Messaging** | Real-time notifications, chat |
| ✅ **Job Queues** | With libraries like `bull` (Node.js) or `celery` (Python) |
| ✅ **Real-Time Analytics** | Count page views, active users |
| ✅ **Distributed Locks** | Prevent race conditions |

---

### 🔹 Example: Rate Limiting

```bash
# Increment request count
INCR user:123:req_count

# Set TTL if first request
NX EX 60  # Only if key doesn't exist, expire in 60s

# Check count
GET user:123:req_count
# If > 100 → reject request
```

✅ 1000s of checks per second.

---

### 📌 When NOT to Use Redis

| Anti-Pattern | Why |
|-------------|-----|
| ❌ **Primary Database** | Risk of data loss if not persisted |
| ❌ **Large Files** | RAM is expensive — use S3 for binaries |
| ❌ **Complex Queries** | No joins, no full-text search |
| ❌ **Long-Term Storage** | Use disk-based DBs (PostgreSQL, MongoDB) |

---

### 📌 Interview Answer

> _"I use Redis for caching, session storage, rate limiting, and real-time features like leaderboards. It's perfect for data that needs sub-millisecond access and can be lost (ephemeral). I don't use it as a primary database due to RAM cost and persistence risks."_  

---

## 20. What data structures does Redis support?

Redis supports **7 core data structures** — making it far more than a simple key-value store.

---

### ✅ 1. **Strings**
- Simple key → value (string, number, JSON)
- Max 512MB
- Commands: `SET`, `GET`, `INCR`, `DECR`

```bash
SET name "Alice"
INCR page_views
```

---

### ✅ 2. **Hashes**
- Key → multiple fields (like a document)
- Efficient for storing objects

```bash
HSET user:123 name "Alice" email "a@x.com" age 25
HGET user:123 name
```

✅ Like a lightweight document store.

---

### ✅ 3. **Lists**
- Ordered collection of strings (linked list)
- Add to head (`LPUSH`) or tail (`RPUSH`)
- Remove and return (`LPOP`, `RPOP`)

```bash
LPUSH tasks "send_email"
RPOP tasks  # Process task
```

✅ Great for **queues** and **stacks**.

---

### ✅ 4. **Sets**
- Unordered collection of **unique** strings
- No duplicates
- Operations: union, intersection, difference

```bash
SADD tags:java "post1" "post2"
SISMEMBER tags:java "post1"  # Check membership
```

✅ Ideal for **tagging**, **followers**, **unique items**.

---

### ✅ 5. **Sorted Sets (ZSet)**
- Like Sets, but each member has a **score** (double)
- Sorted by score
- Commands: `ZADD`, `ZRANGE`, `ZREVRANK`

```bash
ZADD leaderboard 1500 "Alice"
ZADD leaderboard 1200 "Bob"
ZRANGE leaderboard 0 10 WITHSCORES  # Top 10
```

✅ Perfect for **leaderboards**, **priority queues**, **time-series**.

---

### ✅ 6. **Bitmaps**
- Bit-level operations on strings
- Extremely space-efficient
- Commands: `SETBIT`, `GETBIT`, `BITCOUNT`

```bash
# Track daily logins
SETBIT user:123:logins 0 1  # Day 0: logged in
SETBIT user:123:logins 1 0  # Day 1: not logged in
BITCOUNT user:123:logins    # Total logins
```

✅ 1 bit per day → 365 days = ~46 bytes.

---

### ✅ 7. **Streams**
- Append-only log of entries
- Each entry has ID and key-value pairs
- Supports consumer groups (like Kafka)

```bash
XADD mystream * name Alice action login
XREAD COUNT 10 STREAMS mystream 0
```

✅ Used for **event sourcing**, **message queues**, **real-time pipelines**.

---

### 📌 Interview Answer

> _"Redis supports strings, hashes, lists, sets, sorted sets, bitmaps, and streams. I use strings for caching, hashes for objects, lists for queues, sets for tags, sorted sets for leaderboards, bitmaps for compact tracking, and streams for event queues. This makes Redis a Swiss Army knife for real-time data."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine services**:
> _"I use MongoDB for persistent data with sharding and replication, and Redis for caching, sessions, and real-time leaderboards."_  

That shows **deep, integrated NoSQL knowledge**.

---
