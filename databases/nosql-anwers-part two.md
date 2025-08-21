# 📦 MongoDB Deep Dive (Part 2)  
**Tables vs Collections, _id, Queries & Indexing**

This document covers **essential MongoDB concepts** — perfect for **backend developers**, **system designers**, and **interview prep**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 6. What is the equivalent of a "table" and "row" in MongoDB?

> In MongoDB:
- ✅ **"Table" → Collection**
- ✅ **"Row" → Document**

But with key differences due to MongoDB’s **schema-flexible, document-oriented** nature.

---

### 🔹 SQL vs MongoDB Terminology

| SQL (Relational) | MongoDB (Document) | Notes |
|------------------|--------------------|-------|
| **Database** | **Database** | Logical container |
| **Table** | **Collection** | Group of documents (no schema enforcement) |
| **Row** | **Document** | JSON-like record with key-value pairs |
| **Column** | **Field** | Key in a document |
| **Primary Key** | **_id field** | Unique identifier (auto-generated if not provided) |
| **Join** | **Embedding or $lookup** | Denormalization or aggregation |

---

### 🔹 Example: Users Table vs Collection

#### ✅ SQL (MySQL)
```sql
-- Table: users
| id  | name  | email         | age |
|-----|-------|---------------|-----|
| 1   | Alice | alice@x.com   | 25  |
| 2   | Bob   | bob@y.com     | 30  |
```

#### ✅ MongoDB
```json
// Collection: users
[
  {
    "_id": ObjectId("507f1f77bcf86cd799439011"),
    "name": "Alice",
    "email": "alice@x.com",
    "age": 25
  },
  {
    "_id": ObjectId("507f1f77bcf86cd799439012"),
    "name": "Bob",
    "email": "bob@y.com",
    "age": 30
  }
]
```

---

### 🔹 Key Differences

| Feature | SQL Table | MongoDB Collection |
|--------|---------|-------------------|
| **Schema** | Rigid — all rows have same columns | Flexible — documents can have different fields |
| **Joins** | Built-in (`JOIN`) | Manual (`$lookup`) or denormalize |
| **Scaling** | Vertical or complex sharding | Built-in horizontal sharding |
| **Data Model** | Normalized | Often denormalized (embedded data) |

---

### 📌 Interview Answer

> _"In MongoDB, a 'table' is called a collection, and a 'row' is a document. Unlike SQL, collections don't enforce schema — each document can have different fields. I use collections to store JSON-like documents, and design them to minimize joins by embedding related data."_  

---

## 7. What is a collection in MongoDB?

> A **collection** is a **group of MongoDB documents**, similar to a **table in SQL**, but **without a fixed schema**.

It’s the **primary container for data** in a MongoDB database.

---

### ✅ Key Characteristics

| Feature | Description |
|--------|-------------|
| ✅ **Schema-less** | Documents in the same collection can have different fields |
| ✅ **Dynamic Creation** | Created automatically when you insert the first document |
| ✅ **No Joins Required** | Related data can be embedded (e.g., user + orders) |
| ✅ **Index Support** | Can create indexes on any field |
| ✅ **Capped Collections** | Fixed-size, FIFO (useful for logs) |

---

### 🔹 Example: Flexible Schema in Action

```json
// In the same 'users' collection:
{
  "_id": 1,
  "name": "Alice",
  "email": "alice@x.com"
}
{
  "_id": 2,
  "name": "Bob",
  "email": "bob@y.com",
  "age": 30,
  "preferences": { "theme": "dark" }
}
```

✅ No need to `ALTER TABLE` to add new fields.

---

### 🔹 Capped Collection (for Logs)

```javascript
db.createCollection("logs", { capped: true, size: 10000000 })
```

- Fixed size (e.g., 10MB)
- Acts like a **circular buffer** — oldest documents are overwritten
- **Insertion order preserved**
- Great for **audit logs**, **event streams**

---

### 📌 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Use descriptive names** | `user_profiles`, not `u` |
| ✅ **Avoid very large collections** | Consider sharding if > 1TB |
| ✅ **Use capped collections for logs** | Auto-expiration, fast writes |
| ✅ **Don’t put all data in one collection** | Still organize by entity |

---

### 📌 Interview Answer

> _"A collection is a group of documents, like a table but schema-less. I use it to store related data — like all user profiles. Documents can have different fields, which is great for evolving schemas. I use capped collections for logs to auto-expire old data."_  

---

## 8. What is the role of `_id` in a MongoDB document?

> The `_id` field is the **unique identifier** for a document — it’s the **primary key**.

It’s **automatically created** if not provided.

---

### ✅ Key Rules

| Rule | Description |
|------|-------------|
| ✅ **Required** | Every document must have an `_id` |
| ✅ **Unique** | No two documents in a collection can have the same `_id` |
| ✅ **Immutable** | Should not be changed after creation |
| ✅ **Indexed** | Automatically indexed (unique index on `_id`) |
| ✅ **Can be any type** | But `ObjectId` is default |

---

### 🔹 Default: `ObjectId`

```json
"_id": ObjectId("507f1f77bcf86cd799439011")
```

✅ `ObjectId` is a 12-byte value:
- 4 bytes: Unix timestamp
- 3 bytes: Machine identifier
- 2 bytes: Process ID
- 3 bytes: Counter

✅ **Benefits**:
- Globally unique
- Time-sortable (roughly)
- No coordination needed (good for distributed systems)

---

### 🔹 Custom `_id`

You can use your own value:

```json
{ "_id": "user_123", "name": "Alice" }
{ "_id": 1001, "product": "Laptop" }
{ "_id": "alice@x.com", "name": "Alice" }
```

✅ **Use when**:
- You have a natural unique key (email, username)
- Migrating from SQL (`_id` = `user_id`)
- Want human-readable IDs

❌ **Avoid**:
- Sequential integers (security risk, easy to guess)
- Mutable values (e.g., email — if user changes it)

---

### 📌 Interview Answer

> _"The _id field is the primary key — unique and required. MongoDB auto-generates an ObjectId if not provided. ObjectId is 12-byte, time-based, and globally unique — great for distributed systems. I use custom _id when I have a natural key, like user ID, but avoid mutable or guessable values."_  

---

## 9. How do you query data in MongoDB? (e.g., `find()`, `findOne()`)

> MongoDB provides **rich query operators** to retrieve documents.

The main methods are `find()` and `findOne()`.

---

### ✅ `find()` – Get Multiple Documents

```javascript
// Get all users
db.users.find()

// Filter: name = "Alice"
db.users.find({ "name": "Alice" })

// Filter: age > 25
db.users.find({ "age": { "$gt": 25 } })

// AND: name = "Alice" AND age > 25
db.users.find({ "name": "Alice", "age": { "$gt": 25 } })

// OR: name = "Alice" OR name = "Bob"
db.users.find({
  "$or": [
    { "name": "Alice" },
    { "name": "Bob" }
  ]
})

// Projection: return only name and email
db.users.find(
  { "age": { "$gt": 25 } },
  { "name": 1, "email": 1, "_id": 0 }
)
```

✅ Returns a **cursor** — documents are fetched lazily.

---

### ✅ `findOne()` – Get One Document

```javascript
// Get first user with name "Alice"
db.users.findOne({ "name": "Alice" })

// With projection
db.users.findOne(
  { "name": "Alice" },
  { "email": 1, "_id": 0 }
)
```

✅ Returns a **single document** (or `null`), not a cursor.

---

### 🔹 Common Query Operators

| Operator | Meaning | Example |
|--------|--------|--------|
| `$gt` | Greater than | `{ "age": { "$gt": 25 } }` |
| `$gte` | Greater than or equal | `{ "age": { "$gte": 18 } }` |
| `$lt` | Less than | `{ "score": { "$lt": 50 } }` |
| `$in` | In array | `{ "status": { "$in": ["active", "pending"] } }` |
| `$exists` | Field exists | `{ "age": { "$exists": true } }` |
| `$regex` | Regular expression | `{ "email": { "$regex": "@gmail.com$" } }` |
| `$elemMatch` | Match array element | `{ "tags": { "$elemMatch": { "$eq": "java" } } }` |

---

### 📌 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Use indexes** on queried fields | Fast lookups |
| ✅ **Limit results** with `.limit()` | Avoid large result sets |
| ✅ **Use projection** to reduce data transfer | Only fetch needed fields |
| ✅ **Avoid `$where`** | Slow, uses JavaScript engine |

---

### 📌 Interview Answer

> _"I use find() to get multiple documents and findOne() for a single match. I filter with operators like $gt, $in, $regex. I use projection to return only needed fields and limit results for performance. Always index queried fields."_  

---

## 10. How do you create an index in MongoDB?

> **Indexes** improve query performance by **avoiding full collection scans**.

MongoDB supports **single-field**, **compound**, **text**, **geospatial**, and **TTL** indexes.

---

### ✅ Syntax
```javascript
db.collection.createIndex( { field: 1 } )
```

- `1` = ascending
- `-1` = descending

---

### 🔹 1. Single-Field Index
```javascript
// Speed up queries on email
db.users.createIndex( { "email": 1 } )
```

✅ Use for fields in `WHERE`, `SORT`, `JOIN` ($lookup)

---

### 🔹 2. Compound Index
```javascript
// For queries filtering by status and sorting by createdAt
db.orders.createIndex( { "status": 1, "createdAt": -1 } )
```

✅ Order matters: leftmost fields used first

---

### 🔹 3. Unique Index
```javascript
// Prevent duplicate emails
db.users.createIndex( { "email": 1 }, { "unique": true } )
```

✅ Enforces uniqueness at database level

---

### 🔹 4. Text Index (for Search)
```javascript
// Enable text search on name and description
db.products.createIndex( { "name": "text", "description": "text" } )

// Search
db.products.find( { "$text": { "$search": "laptop" } } )
```

✅ Supports language-aware stemming

---

### 🔹 5. TTL Index (Auto-Expiring Data)
```javascript
// Delete documents after 24 hours
db.sessions.createIndex( { "createdAt": 1 }, { "expireAfterSeconds": 86400 } )
```

✅ Perfect for **caching**, **sessions**, **logs**

---

### 🔹 6. Partial Index (Conditional)
```javascript
// Only index active users
db.users.createIndex(
  { "email": 1 },
  { "partialFilterExpression": { "active": true } }
)
```

✅ Saves space and improves performance

---

### 📌 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Index fields in queries** | Especially in `WHERE` and `SORT` |
| ✅ **Use compound indexes wisely** | Match query patterns |
| ✅ **Avoid too many indexes** | Slows down writes |
| ✅ **Use TTL for temporary data** | Auto-cleanup |
| ✅ **Monitor with `explain()`** | Check if index is used |

```javascript
db.users.find({ email: "a@x.com" }).explain("executionStats")
```

---

### 📌 Interview Answer

> _"I create indexes with createIndex(). I use single-field for common filters, compound for multi-field queries, and unique to enforce constraints. I also use text indexes for search and TTL for auto-expiring data like sessions. I avoid over-indexing — each index slows down inserts."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I store user data in a MongoDB collection, use _id for unique lookup, query with find(), and create indexes on email and status for performance."_  

That shows **deep, practical MongoDB knowledge**.

---

