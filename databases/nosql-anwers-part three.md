# 📦 MongoDB & NoSQL Deep Dive (Part 3)  
**Aggregation, CAP Theorem, Transactions & Consistency**

This document covers **advanced NoSQL concepts** — essential for **system design**, **distributed systems**, and **senior backend roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 11. What is the aggregation pipeline in MongoDB?

> The **aggregation pipeline** is a **framework for data transformation** in MongoDB — like **ETL (Extract, Transform, Load)** within the database.

It processes documents through a series of **stages**, each performing an operation like filtering, grouping, or sorting.

---

### ✅ How It Works

```
[Input Documents] → Stage 1 → Stage 2 → ... → [Final Output]
```

Each stage:
- Takes a stream of documents
- Performs an operation
- Outputs a new stream

---

### ✅ Key Stages

| Stage | Purpose | Example |
|------|--------|--------|
| `$match` | Filter documents (like `WHERE`) | `{ $match: { status: "active" } }` |
| `$project` | Reshape documents (like `SELECT`) | `{ $project: { name: 1, email: 1 } }` |
| `$group` | Group documents (like `GROUP BY`) | `{ $group: { _id: "$status", count: { $sum: 1 } } }` |
| `$sort` | Sort documents | `{ $sort: { createdAt: -1 } }` |
| `$limit` | Limit number of documents | `{ $limit: 10 }` |
| `$lookup` | Join with another collection | Left outer join |
| `$unwind` | Deconstruct arrays | Turn array elements into documents |
| `$addFields` | Add computed fields | `{ $addFields: { total: { $sum: "$items.price" } } }` |

---

### ✅ Example: Sales Report

```javascript
db.orders.aggregate([
  // 1. Only active orders
  { $match: { status: "completed" } },

  // 2. Extract date parts
  { $addFields: {
      orderDate: { $dateToString: { format: "%Y-%m-%d", date: "$createdAt" } }
  }},

  // 3. Group by date and sum amounts
  { $group: {
      _id: "$orderDate",
      totalSales: { $sum: "$amount" },
      orderCount: { $sum: 1 }
  }},

  // 4. Sort by date (newest first)
  { $sort: { _id: -1 } },

  // 5. Top 7 days
  { $limit: 7 }
])
```

✅ Output: Daily sales report for the last week.

---

### 🔹 Performance Tips

| Rule | Why |
|------|-----|
| ✅ **Put `$match` and `$limit` early** | Reduce data early |
| ✅ **Index fields used in `$match`, `$sort`** | Faster execution |
| ✅ **Avoid unnecessary stages** | Keep pipeline lean |
| ✅ **Use `$lookup` sparingly** | Can be slow — prefer embedding |

---

### 📌 Interview Answer

> _"The aggregation pipeline transforms data through stages like $match, $group, $sort. I use it for reports, analytics, and ETL-like operations. I optimize by filtering early, indexing key fields, and minimizing $lookup. It’s MongoDB’s answer to complex SQL queries."_  

---

## 12. What is the difference between MongoDB and MySQL?

| Feature | **MongoDB** | **MySQL** |
|--------|-------------|----------|
| **Type** | Document database (NoSQL) | Relational database (SQL) |
| **Data Model** | JSON-like documents (BSON) | Tables with rows and columns |
| **Schema** | Flexible (schema-less) | Rigid (schema-enforced) |
| **Joins** | Manual (`$lookup`) or denormalized | Built-in (`JOIN`) |
| **Transactions** | Multi-document (4.0+) — limited scalability | Full ACID across tables |
| **Scaling** | Horizontal (sharding) | Vertical or complex sharding |
| **Query Language** | MongoDB Query Language (JSON-based) | SQL (declarative) |
| **Use Case** | Unstructured data, rapid iteration, scale | Structured data, complex queries, consistency |
| **Consistency** | Eventual (by default) | Strong (by default) |
| **Indexing** | Rich: text, geospatial, TTL, partial | B-tree, full-text, spatial |

---

### 🔹 When to Use Which?

| Use Case | Choice |
|--------|--------|
| ✅ **User profiles, content, catalogs** | MongoDB |
| ✅ **Orders, payments, banking** | MySQL |
| ✅ **Real-time analytics, IoT** | MongoDB |
| ✅ **Reporting, complex joins** | MySQL |
| ✅ **High write throughput** | MongoDB |
| ✅ **Strong consistency required** | MySQL |

---

### 🔹 Example: User + Orders

#### ✅ MongoDB (Embedded)
```json
{
  "_id": "123",
  "name": "Alice",
  "orders": [
    { "id": "A1", "amount": 99.99 },
    { "id": "A2", "amount": 49.99 }
  ]
}
```

✅ Fast reads, no joins  
❌ Hard to query all orders

#### ✅ MySQL (Normalized)
```sql
Users:     | id | name  |
Orders:    | id | user_id | amount |
```

✅ Flexible queries, referential integrity  
❌ Joins needed, slower at scale

---

### 📌 Interview Answer

> _"MongoDB is flexible, scales horizontally, and stores JSON-like documents — great for unstructured data. MySQL is structured, supports complex joins and strong consistency — ideal for financial systems. I use MongoDB for user profiles and content, MySQL for transactions and reporting."_  

---

## 13. What is eventual consistency in NoSQL?

> **Eventual consistency** means that **if no new updates are made**, all reads will **eventually return the last updated value**.

It’s a **relaxation of strong consistency** to achieve **availability and partition tolerance**.

---

### 🔹 How It Works

```
[Write to Node A] → Replicated to Node B and C → Eventually all nodes agree
```

But during replication:
- A read to **Node B** might return **old data**
- After replication completes → all nodes return new data

---

### 🔹 Example: Social Media Feed

1. You post: "Hello World!"
2. System writes to **primary node**
3. Follower in New York reads from **replica in NY** → sees old feed (no post)
4. 1 second later → replica syncs → now sees post

✅ Trade-off: **availability** over **immediate consistency**

---

### 🔹 When to Use Eventual Consistency

| Use Case | Why |
|--------|-----|
| ✅ **Social Feeds** | Users don’t need instant update |
| ✅ **Product Catalogs** | Price changes can lag |
| ✅ **Analytics** | Aggregates updated periodically |
| ✅ **Caching** | Redis, Memcached |

---

### 🔹 Challenges

| Risk | Mitigation |
|------|-----------|
| ❌ **Stale Reads** | Use session consistency, read-your-writes |
| ❌ **User Confusion** | Show "posting..." while syncing |
| ❌ **Race Conditions** | Use versioning, optimistic locking |

---

### 📌 Interview Answer

> _"Eventual consistency means data will become consistent after updates stop. I use it in distributed systems like MongoDB or DynamoDB to ensure availability during network partitions. It’s fine for social feeds or catalogs, but not for banking. I mitigate stale reads with session consistency or versioning."_  

---

## 14. What is the CAP theorem? Explain its three components.

> The **CAP Theorem** (Brewer’s Theorem) states that in a **distributed system**, you can only guarantee **two out of three** of the following:

| Property | Meaning |
|---------|--------|
| ✅ **C: Consistency** | Every read receives the most recent write or an error |
| ✅ **A: Availability** | Every request receives a response (success or failure) — no downtime |
| ✅ **P: Partition Tolerance** | System continues to operate despite network partitions (message loss) |

> ⚠️ **P is mandatory** in distributed systems — so you choose between **CP** and **AP**.

---

### 🔹 Real-World Trade-Offs

| System | CAP Choice | Why |
|-------|-----------|-----|
| **MongoDB (Replica Set)** | **CP** | During partition, primary steps down → no writes until majority |
| **Cassandra** | **AP** | All nodes accept writes → eventual consistency |
| **Amazon DynamoDB** | **AP** | Prioritizes availability and latency |
| **Google Spanner** | **CP** | Uses atomic clocks for strong consistency |
| **Redis (single node)** | **CA** | Not distributed — no partition tolerance |

---

### 🔹 Example: Network Partition

```
[User] → [Load Balancer] → [Node A (US)] ← Network Break → [Node B (EU)]
```

- User writes to Node A → succeeds
- User in EU reads from Node B → gets old data (eventual) or error (strong)

✅ **AP**: Return old data (available but inconsistent)  
✅ **CP**: Return error (consistent but not available)

---

### 📌 Interview Answer

> _"CAP theorem says in a distributed system, you can only have 2 of 3: Consistency, Availability, Partition Tolerance. Since networks can fail, P is mandatory — so you choose CP or AP. MongoDB is CP — during a partition, it becomes read-only. Cassandra is AP — all nodes stay available but may return stale data."_  

---

## 15. How does MongoDB handle transactions?

> MongoDB supports **multi-document ACID transactions** (since version 4.0), but with **trade-offs** compared to traditional SQL databases.

---

### ✅ What MongoDB Transactions Support

| Feature | Supported? |
|--------|-----------|
| ✅ **ACID Guarantees** | Atomicity, Consistency, Isolation, Durability |
| ✅ **Multi-Document** | Across multiple collections, databases (4.2+) |
| ✅ **Sessions** | Required to group operations |
| ✅ **Rollback on Error** | All operations in transaction are undone |
| ✅ **Isolation Level** | Snapshot isolation (by default) |

---

### ✅ Example: Bank Transfer

```javascript
const session = db.getMongo().startSession();
session.startTransaction();

try {
  const users = session.getDatabase("bank").getCollection("users");

  users.updateOne(
    { _id: "alice" },
    { $inc: { balance: -100 } }
  );

  users.updateOne(
    { _id: "bob" },
    { $inc: { balance: 100 } }
  );

  session.commitTransaction();
} catch (error) {
  session.abortTransaction();
  throw error;
}
```

✅ Either both updates succeed, or both are rolled back.

---

### 🔹 Limitations

| Limitation | Why |
|----------|-----|
| ❌ **Not for High-Throughput** | Performance overhead — not for 1000s of transactions/sec |
| ❌ **Max 60 Seconds** | Transactions time out after 60s |
| ❌ **No Cross-Cluster Transactions** | Limited to one replica set or sharded cluster |
| ❌ **Higher Memory Usage** | Snapshots held during transaction |

---

### 🔹 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Use for Business-Critical Operations** | Transfers, order placement |
| ✅ **Keep Transactions Short** | Under 10s if possible |
| ✅ **Avoid Long-Running Transactions** | Risk of timeout and memory pressure |
| ✅ **Use Retry Logic** | For transient errors |
| ✅ **Prefer Document Model** | Embed related data to avoid transactions |

---

### 📌 Interview Answer

> _"MongoDB supports multi-document ACID transactions using sessions. I use them for critical operations like fund transfers. But they have limits: 60-second timeout, performance cost. I prefer embedding data to avoid transactions when possible. For high-throughput systems, I use event sourcing or eventual consistency."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use MongoDB for scalability and flexibility. It’s AP in CAP, uses eventual consistency, but supports transactions for critical operations. I avoid them when possible by embedding data."_  

That shows **deep, integrated NoSQL knowledge**.

---
