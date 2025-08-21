# 📦 NoSQL Deep Dive (Part 5)  
**Redis Caching, TTL, Cassandra, Eventual Consistency & NoSQL Advantages**

This document covers **advanced NoSQL concepts** — essential for **performance**, **scalability**, and **system design interviews**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 21. How is Redis used for caching?

> **Redis** is one of the **most popular caching solutions** due to its **in-memory speed**, **rich data structures**, and **TTL support**.

It’s used to **reduce database load** and **improve response times**.

---

### 🔹 Common Caching Patterns

#### ✅ 1. **Cache-Aside (Lazy Loading)**
```text
[App] → Check Redis → 
    ✅ Hit: Return from cache
    ❌ Miss: Read DB → Write to Redis → Return
```

```java
public User getUser(Long id) {
    String key = "user:" + id;
    
    // Try cache first
    String cached = redis.get(key);
    if (cached != null) {
        return deserialize(cached);
    }

    // Cache miss: read from DB
    User user = userRepository.findById(id);
    if (user != null) {
        redis.setex(key, 3600, serialize(user)); // Cache for 1 hour
    }
    return user;
}
```

✅ Most common pattern  
✅ App controls cache lifecycle

---

#### ✅ 2. **Write-Through**
```text
[App] → Write to Redis → Redis writes to DB → Ack to App
```

✅ Data always consistent  
✅ Slower (writes go through Redis)

---

#### ✅ 3. **Write-Behind (Write-Back)**
```text
[App] → Write to Redis → Redis queues DB write → Async DB update
```

✅ Fast writes  
✅ Risk of data loss if Redis crashes before DB write

---

### 🔹 Real-World Use Cases

| Use Case | How |
|--------|-----|
| ✅ **Database Query Results** | Cache `getUserById`, `getProductCatalog` |
| ✅ **Session Storage** | Store user session in Redis with TTL |
| ✅ **Full Page Caching** | Cache HTML of popular pages |
| ✅ **API Response Caching** | Cache `/api/users` response |
| ✅ **Rate Limiting** | Track requests with `INCR` + `EXPIRE` |

---

### 🔹 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Use appropriate TTL** | Prevent stale data, memory bloat |
| ✅ **Handle cache misses gracefully** | Fall back to DB |
| ✅ **Use cache stampede protection** | Add random TTL jitter to avoid mass expiry |
| ✅ **Monitor hit rate** | Low hit rate → reevaluate cache strategy |
| ✅ **Use Redis Cluster** | For high availability and scalability |

---

### 📌 Interview Answer

> _"I use Redis for caching with the cache-aside pattern: check Redis first, if miss, read DB and populate cache. I set TTL to avoid stale data. I cache database queries, sessions, and API responses. It reduces database load and improves latency from 100ms to 1ms."_  

---

## 22. What is TTL in Redis?

> **TTL (Time To Live)** is the **expiration time** for a Redis key — after which it is **automatically deleted**.

It’s essential for **ephemeral data** like sessions, caches, and rate limits.

---

### ✅ Commands

| Command | Purpose |
|--------|--------|
| `EXPIRE key 60` | Set TTL to 60 seconds |
| `EXPIREAT key 1672531200` | Expire at Unix timestamp |
| `TTL key` | Check remaining TTL (-1 = no TTL, -2 = expired) |
| `PERSIST key` | Remove TTL (make persistent) |
| `SET key value EX 3600` | Set with TTL in seconds |
| `SETEX key 3600 value` | Set + expire in one command |

---

### ✅ Example: Session Management

```bash
# User logs in
SET session:abc123:user_id "123" EX 3600  # Expire in 1 hour

# Check session
TTL session:abc123:user_id  # Returns 3599, 3598, ...
# After 1 hour → key gone
```

✅ No cleanup job needed — Redis auto-deletes.

---

### ✅ Example: Rate Limiting

```bash
# On each request
INCR user:123:req_count
# Only set TTL if key is new (first request in window)
NX EX 60  # Expire in 60 seconds

# If req_count > 100 → reject request
```

✅ Simple, fast, self-cleaning.

---

### 🔹 TTL Best Practices

| Rule | Why |
|------|-----|
| ✅ **Set TTL on all cache keys** | Prevent memory leaks |
| ✅ **Add jitter to TTL** | Avoid "thundering herd" when keys expire together |
| ✅ **Use `EX` with `SET`** | Atomic set + expire |
| ✅ **Monitor memory usage** | Use `INFO memory` |
| ✅ **Use Redis eviction policies** | `volatile-lru`, `allkeys-lru` when full |

---

### 📌 Interview Answer

> _"TTL sets an expiration time for a Redis key. I use it for sessions, caches, and rate limits. After TTL, Redis auto-deletes the key. I use EXPIRE or SETEX to set it. I add jitter to avoid mass expiry. It’s essential for managing ephemeral data and preventing memory bloat."_  

---

## 23. What is Cassandra? What are its key features?

> **Apache Cassandra** is a **highly scalable, distributed NoSQL database** designed for **high availability** and **no single point of failure**.

It’s used by **Netflix, Apple, Uber** for massive-scale applications.

---

### 🔹 Key Features

| Feature | Description |
|--------|-------------|
| ✅ **Masterless Architecture** | All nodes are equal — no single point of failure |
| ✅ **Linear Scalability** | Add nodes → linear increase in throughput |
| ✅ **High Write Throughput** | Optimized for fast writes (logging, IoT) |
| ✅ **Tunable Consistency** | Choose consistency level per query (ONE, QUORUM, ALL) |
| ✅ **Eventual Consistency** | AP in CAP theorem |
| ✅ **Column-Family Data Model** | Wide-column store (like Bigtable) |
| ✅ **Multi-Datacenter Replication** | Built-in support for geo-distribution |
| ✅ **CQL (Cassandra Query Language)** | SQL-like syntax |

---

### 🔹 Data Model: Wide-Column Store

```cql
-- Table: user_playback
-- Primary Key: (user_id, timestamp)
 user_id | timestamp           | video_id | duration
---------+---------------------+----------+----------
  123   | 2025-04-05 10:00:00 | V1       | 3600
  123   | 2025-04-05 10:01:00 | V2       | 1800
```

✅ Efficient for time-series data  
✅ Rows can have different columns

---

### 🔹 Use Cases

| Use Case | Why Cassandra? |
|--------|---------------|
| ✅ **Time-Series Data** | Sensor data, logs, metrics |
| ✅ **Messaging Systems** | High write volume |
| ✅ **Recommendation Engines** | Fast reads/writes |
| ✅ **IoT Platforms** | Millions of devices writing data |
| ✅ **Content Management** | Global content delivery |

---

### 📌 Interview Answer

> _"Cassandra is a distributed, masterless NoSQL database built for high availability and scalability. It uses a wide-column model and is AP in CAP. I use it for time-series data, logging, and high-write systems like IoT. It scales linearly and supports multi-datacenter replication."_  

---

## 24. What is eventual consistency, and which NoSQL databases use it?

> **Eventual consistency** means that **if no new updates are made**, all reads will **eventually return the last updated value**.

It’s a **trade-off** to achieve **availability** and **partition tolerance** in distributed systems.

---

### 🔹 How It Works

```
[Write to Node A] → Replicated to B, C → Eventually all agree
```

But during replication:
- A read to **Node B** might return **old data**
- After sync → all nodes return new data

✅ Acceptable for many apps: social feeds, catalogs, analytics

---

### 🔹 NoSQL Databases That Use Eventual Consistency

| Database | Default Consistency Model |
|--------|--------------------------|
| ✅ **Cassandra** | Eventual (tunable to strong) |
| ✅ **DynamoDB** | Eventual (strong consistency optional) |
| ✅ **Couchbase** | Eventual |
| ✅ **Riak** | Eventual |
| ✅ **Redis (replicated)** | Eventual (async replication) |
| ✅ **MongoDB (multi-region)** | Eventual (unless strong read preference) |

> ❌ **MongoDB (single replica set)**: Strong consistency by default (reads from primary)

---

### 🔹 Example: Social Media Post

1. User posts "Hello!" → written to **US node**
2. Follower in **EU** reads from **EU replica** → sees old feed
3. 500ms later → replica syncs → now sees post

✅ Trade-off: **availability** over **immediate consistency**

---

### 🔹 Benefits

| Benefit | Explanation |
|--------|-------------|
| ✅ **High Availability** | All nodes accept reads/writes during network issues |
| ✅ **Low Latency** | Read from nearest node, even if stale |
| ✅ **Scalability** | No coordination needed for every write |

---

### 📌 Interview Answer

> _"Eventual consistency means data will become consistent after updates stop. It's used in distributed NoSQL databases like Cassandra, DynamoDB, and Redis to ensure availability during network partitions. I use it for social feeds and catalogs where immediate consistency isn't critical. For banking, I use strong consistency."_  

---

## 25. What are the advantages of NoSQL over SQL?

| Advantage | Explanation | Example |
|----------|-------------|--------|
| ✅ **Flexible Schema** | No rigid schema — add fields without migrations | Add `preferences` to user without `ALTER TABLE` |
| ✅ **Horizontal Scalability** | Scale out by adding more servers | Handle 1M requests/sec with sharding |
| ✅ **High Performance** | Optimized for specific workloads (e.g., key-value, document) | Redis: 100K ops/sec in memory |
| ✅ **Better for Unstructured Data** | JSON, logs, sensor data | Store nested user preferences in MongoDB |
| ✅ **High Availability & Fault Tolerance** | Built-in replication, no single point of failure | Cassandra: all nodes equal |
| ✅ **Developer Productivity** | JSON-native, maps to objects | No ORM mapping for MongoDB |
| ✅ **Cost-Effective at Scale** | Use commodity hardware | Scale to petabytes cheaper than SQL |

---

### 🔹 When NoSQL Shines

| Scenario | Why NoSQL Wins |
|--------|---------------|
| ❌ **Rapid Iteration** | No schema migrations |
| ❌ **Big Data** | Petabytes of logs, IoT data |
| ❌ **Global Apps** | Geo-distributed, low-latency access |
| ❌ **Real-Time Analytics** | Fast writes, aggregation |
| ❌ **High Write Throughput** | 10K+ writes/sec |

---

### 📌 Interview Answer

> _"NoSQL offers flexible schema, horizontal scaling, and high performance for unstructured data. I use it when I need to scale rapidly, handle JSON natively, or support high write throughput. It's ideal for modern apps with evolving data models. But I still use SQL for complex transactions and strong consistency."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use Redis for caching with TTL, MongoDB for flexible document storage, and Cassandra for high-write time-series data — all eventually consistent for availability."_  

That shows **deep, integrated NoSQL knowledge**.

---

