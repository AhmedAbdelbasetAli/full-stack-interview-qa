# 🌐 System Design Deep Dive  
**SQL vs NoSQL, Message Queuing, Brokers, Rate Limiting & Algorithms**

This document covers **essential system design concepts** — critical for **software engineering interviews**, **architecture design**, and **scalable backend development**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 1. What is the difference between SQL and NoSQL in system design?

> The choice between **SQL** and **NoSQL** is one of the **most important decisions** in system design — it affects **scalability**, **consistency**, and **data modeling**.

---

### 🔹 Key Differences

| Feature | **SQL (Relational)** | **NoSQL (Non-Relational)** |
|--------|------------------------|----------------------------|
| **Data Model** | Tables with rows and columns | Document, Key-Value, Column, Graph |
| **Schema** | Rigid (schema-on-write) | Flexible (schema-on-read) |
| **Scaling** | Vertical (scale up) or complex sharding | Horizontal (scale out) |
| **Consistency** | Strong (ACID) | Eventual (AP in CAP) or tunable |
| **Joins** | Built-in support | Manual (`$lookup`) or denormalized |
| **Transactions** | Full ACID across tables | Limited (single doc or lightweight) |
| **Use Case** | Structured data, complex queries | Unstructured data, high scale, flexibility |
| **Examples** | PostgreSQL, MySQL, Oracle | MongoDB, Redis, Cassandra, DynamoDB |

---

### 🔹 When to Use Which?

| Use Case | Choice |
|--------|--------|
| ✅ **User Accounts, Orders, Payments** | SQL |
| ✅ **High Write Throughput (IoT, Logs)** | NoSQL |
| ✅ **Flexible Schema (User Profiles)** | NoSQL |
| ✅ **Real-Time Analytics** | NoSQL (e.g., Cassandra) |
| ✅ **Caching, Sessions** | NoSQL (Redis) |
| ✅ **Complex Reports, Financial Systems** | SQL |
| ✅ **Global Apps with Low Latency** | NoSQL (geo-distributed) |

---

### 🔹 Example: Social Media App

| Component | Database |
|----------|----------|
| User Profiles | **NoSQL (MongoDB)** – flexible schema |
| Posts & Feeds | **NoSQL (Cassandra)** – high write scale |
| Followers | **NoSQL (Redis Sets)** – fast membership checks |
| Payments | **SQL (PostgreSQL)** – strong consistency |

✅ **Hybrid approach** is common in real systems.

---

### 📌 Interview Answer

> _"SQL is best for structured data with relationships and strong consistency — like orders and payments. NoSQL excels at scalability, flexible schema, and high write throughput — like user profiles and real-time feeds. In system design, I choose based on data model, access patterns, and consistency needs. For large systems, I often use both."_  

---

## 2. What is message queuing? When would you use it?

> **Message queuing** is a **pattern** where components communicate **asynchronously** by sending messages to a **queue**.

It **decouples producers and consumers**, improving **resilience**, **scalability**, and **performance**.

---

### 🔹 How It Works

```
[Producer] → [Message Queue] → [Consumer]
```

- **Producer**: Sends messages (e.g., "User signed up")
- **Queue**: Stores messages until processed
- **Consumer**: Processes messages (e.g., "Send welcome email")

✅ **Asynchronous** — producer doesn’t wait

---

### 🔹 When to Use Message Queuing

| Use Case | Why |
|--------|-----|
| ✅ **Background Processing** | Send emails, generate reports |
| ✅ **Load Leveling** | Smooth traffic spikes (e.g., Black Friday) |
| ✅ **Microservices Communication** | Decouple services |
| ✅ **Event-Driven Architecture** | React to events (e.g., "order placed") |
| ✅ **Retry & Fault Tolerance** | Failed messages can be retried |
| ✅ **Batch Processing** | Process messages in batches for efficiency |

---

### 🔹 Example: E-Commerce Order

```text
[Web App] → "Order Placed" → [Queue] → [Email Service] → Send confirmation
                                   ↓
                             [Inventory Service] → Update stock
                                   ↓
                             [Analytics Service] → Log event
```

✅ One event → multiple consumers  
✅ If email service fails, message stays in queue

---

### 📌 Interview Answer

> _"Message queuing allows asynchronous communication between services. I use it to decouple components — like sending emails after an order. It improves scalability, fault tolerance, and resilience. If a service fails, messages are retried. It's essential for event-driven systems."_  

---

## 3. What are common message brokers (e.g., Kafka, RabbitMQ)?

> A **message broker** is a **service** that manages message queues — routing, storing, and delivering messages.

---

### 🔹 Comparison of Popular Brokers

| Broker | Type | Use Case | Key Features |
|--------|------|--------|-------------|
| **RabbitMQ** | General-purpose | Task queues, RPC, microservices | Flexible routing (exchanges), easy to use |
| **Apache Kafka** | Streaming Platform | Real-time pipelines, event sourcing | High throughput, persistent logs, replayable |
| **Amazon SQS** | Managed Queue | Simple decoupling, serverless | Fully managed, scalable, pay-per-use |
| **Amazon SNS** | Pub/Sub | Fan-out to multiple services | Push model, integrates with SQS |
| **Google Pub/Sub** | Global Messaging | Cross-region apps | Global availability, exactly-once delivery |
| **Redis (with Streams)** | Lightweight | Simple queues, real-time | In-memory, low latency |

---

### 🔹 RabbitMQ vs Kafka

| Feature | RabbitMQ | Kafka |
|--------|---------|-------|
| **Model** | Message Queue | Log Streaming |
| **Throughput** | ~10K msgs/sec | ~1M+ msgs/sec |
| **Latency** | Low | Very low |
| **Persistence** | Optional | Always (disk-based) |
| **Message Replay** | ❌ No | ✅ Yes (retain logs) |
| **Use Case** | Task queues, RPC | Event sourcing, real-time analytics |

---

### 🔹 Example: Choose Based on Use Case

| Need | Choice |
|------|--------|
| ✅ "Send email after signup" | RabbitMQ or SQS |
| ✅ "Real-time analytics pipeline" | Kafka |
| ✅ "Fan-out to 10 services" | SNS + SQS |
| ✅ "Replay events for debugging" | Kafka |
| ✅ "Simple in-memory queue" | Redis Streams |

---

### 📌 Interview Answer

> _"RabbitMQ is great for task queues and RPC with flexible routing. Kafka is for high-throughput, durable event streams — like real-time analytics. I use SQS for serverless apps, SNS for fan-out. Kafka supports replay, RabbitMQ doesn’t. I choose based on throughput, durability, and replay needs."_  

---

## 4. What is rate limiting? Why is it important?

> **Rate limiting** restricts the number of requests a user/IP/API key can make in a given time window.

---

### 🔹 Why It’s Important

| Benefit | Explanation |
|--------|-------------|
| ✅ **Prevent Abuse** | Stop bots, scrapers, brute force attacks |
| ✅ **Protect Backend** | Avoid overload during traffic spikes |
| ✅ **Ensure Fair Usage** | Prevent one user from monopolizing resources |
| ✅ **Monetization** | Enforce API tiers (free vs paid) |
| ✅ **Security** | Mitigate DDoS, credential stuffing |

---

### 🔹 Example: API Rate Limits

```http
GET /api/users
Rate-Limit: 1000; window=3600; remaining=998
```

- 1000 requests per hour
- 998 left
- If exceeded → `429 Too Many Requests`

---

### 📌 Interview Answer

> _"Rate limiting controls how many requests a client can make — like 1000/hour. It protects my API from abuse, ensures fair usage, and prevents backend overload. I return 429 and headers like RateLimit-Remaining to help clients. It's essential for public APIs."_  

---

## 5. What are common rate-limiting algorithms?

| Algorithm | How It Works | Pros | Cons |
|----------|-------------|------|------|
| ✅ **Token Bucket** | Tokens refill over time; each request consumes one | Smooth bursts allowed | Slightly complex |
| ✅ **Leaky Bucket** | Requests processed at fixed rate; excess queued or dropped | Smooths traffic | Can delay requests |
| ✅ **Fixed Window** | Count requests per time window (e.g., 60/min) | Simple | Thundering herd at window boundary |
| ✅ **Sliding Window** | Weighted count across two windows | Smoother than fixed | More memory |
| ✅ **Sliding Log** | Keep timestamp of each request | Most accurate | High memory usage |

---

### 🔹 1. Token Bucket

```text
Bucket capacity: 100 tokens
Refill rate: 10 tokens/sec

User makes 15 requests → 15 tokens consumed
Next second: 10 tokens added → 95/100
```

✅ Allows **bursty traffic** within limits  
✅ Used by AWS, Stripe

---

### 🔹 2. Sliding Window (Recommended)

```text
Window: 60 seconds
Now: 10:05:30

Count = (requests in last 60 sec) 
       = (full last minute) + (partial current minute weighted)
```

✅ Avoids spike at window boundary  
✅ Used by most modern APIs

---

### 🔹 Implementation with Redis (Sliding Window)

```java
// Pseudocode
String key = "rate_limit:user_123";
long now = System.currentTimeMillis();

// Remove old timestamps
redis.zremrangeByScore(key, 0, now - 60000);

// Add current request
redis.zadd(key, now, now);

// Get count
Long count = redis.zcard(key);

if (count > 100) {
    throw new TooManyRequestsException();
}
```

✅ Use `ZSET` to store timestamps.

---

### 📌 Interview Answer

> _"I use sliding window algorithm for rate limiting — it's smoother than fixed window. Token bucket allows bursts. I store counts in Redis with timestamps. For high scale, I use a distributed rate limiter. I return 429 and headers to guide clients."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use Kafka for event-driven order processing, SQL for payments, NoSQL for user data, and rate limiting to protect my API."_  

That shows **deep, integrated system design knowledge**.

---


