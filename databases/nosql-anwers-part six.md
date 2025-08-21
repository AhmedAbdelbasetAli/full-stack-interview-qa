# 📦 NoSQL Deep Dive (Part 6)  
**NoSQL Downsides, MongoDB Relationships, Security & SQL vs NoSQL**

This document covers **advanced NoSQL trade-offs** — essential for **informed decision-making**, **system design**, and **senior backend roles**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 26. What are the disadvantages of NoSQL?

While NoSQL offers **flexibility and scalability**, it comes with **trade-offs**.

---

### 🔹 Key Disadvantages

| Disadvantage | Explanation | Example |
|-------------|-------------|--------|
| ❌ **No Standard Query Language** | Each database has its own API/query language | MongoDB uses CQL-like syntax, Redis uses commands |
| ❌ **Eventual Consistency** | Data may be stale during replication | User doesn’t see their post immediately |
| ❌ **Limited Transactions** | Multi-document transactions are slow or limited | MongoDB supports them but not at high scale |
| ❌ **No Joins (or Expensive)** | Must denormalize or use `$lookup` | Hard to query "orders with user info" |
| ❌ **Memory/Storage Overhead** | Replication, indexing, caching use more resources | Redis stores everything in RAM |
| ❌ **Lack of ACID Guarantees** | Not suitable for financial systems | Can’t ensure balance consistency across transfers |
| ❌ **Complex Data Modeling** | No enforced schema → poor design risks | Duplicate data, inconsistent structure |
| ❌ **Maturity & Tooling** | Fewer mature tools vs SQL (ORMs, BI connectors) | Limited support in legacy reporting tools |

---

### 🔹 Real-World Pain Points

| Scenario | Risk |
|--------|------|
| ❌ **Migrating from SQL** | Developers expect joins and transactions |
| ❌ **Reporting & Analytics** | Hard to run complex aggregations across entities |
| ❌ **Data Integrity** | No foreign key constraints → orphaned data |
| ❌ **Team Learning Curve** | New paradigms (eventual consistency, sharding) |

---

### ✅ When the Disadvantages Matter Most

| Use Case | Why SQL Might Be Better |
|--------|------------------------|
| ✅ **Banking Systems** | Need strong consistency, ACID |
| ✅ **ERP, CRM** | Complex relationships, reporting |
| ✅ **Regulated Industries** | Audit trails, referential integrity |
| ✅ **Legacy Integration** | Tools expect SQL |

---

### 📌 Interview Answer

> _"NoSQL has downsides: no standard query language, eventual consistency, limited transactions, and no joins. It’s hard to ensure data integrity and run complex reports. I use it when scalability and flexibility are key, but avoid it for financial systems or when strong consistency is required."_  

---

## 27. How do you handle relationships (e.g., one-to-many) in MongoDB?

> MongoDB is **non-relational**, so **relationships are not enforced** like in SQL.

You model them using **embedding** or **referencing**.

---

### 🔹 Example: User → Orders (One-to-Many)

#### ✅ Option 1: **Embedding** (Denormalization)
```json
{
  "_id": "user_123",
  "name": "Alice",
  "orders": [
    {
      "orderId": "A1",
      "amount": 99.99,
      "status": "shipped"
    },
    {
      "orderId": "A2",
      "amount": 49.99,
      "status": "pending"
    }
  ]
}
```

✅ **Pros**:  
- Fast reads — one query  
- Atomic updates (with transactions)  
- Natural for JSON APIs  

❌ **Cons**:  
- Document size limit (16MB)  
- Redundant data if orders are large  
- Hard to query all orders globally  

---

#### ✅ Option 2: **Referencing** (Normalized)
```json
// users collection
{
  "_id": "user_123",
  "name": "Alice"
}

// orders collection
{
  "_id": "A1",
  "userId": "user_123",
  "amount": 99.99,
  "status": "shipped"
}
```

✅ **Pros**:  
- No size limits  
- Avoids duplication  
- Easy to query all orders  

❌ **Cons**:  
- Requires **two queries** (or `$lookup`)  
- No referential integrity (orphaned orders)  
- Slower than embedding  

---

### 📌 When to Use Which?

| Use Case | Choice |
|--------|--------|
| ✅ **Few, small related items** | Embed (e.g., user + addresses) |
| ✅ **Large or growing list** | Reference (e.g., user + thousands of logs) |
| ✅ **Shared data** | Reference (e.g., product referenced by many orders) |
| ✅ **Need global queries** | Reference |

---

### 📌 Interview Answer

> _"I handle one-to-many in MongoDB with embedding or referencing. I embed when the child data is small and accessed with the parent — like user preferences. I reference when data is large or shared — like orders. I avoid joins with $lookup unless necessary."_  

---

## 28. What is embedding vs referencing in MongoDB?

| Feature | **Embedding** | **Referencing** |
|--------|---------------|-----------------|
| **Data Location** | Inside parent document | Separate collection |
| **Query Performance** | ✅ Fast (one read) | ❌ Slower (multiple reads or $lookup) |
| **Document Size** | ❌ Risk of 16MB limit | ✅ No limit |
| **Data Duplication** | ✅ Yes (denormalized) | ❌ No |
| **Atomicity** | ✅ Single-document updates | ❌ Requires transactions |
| **Use Case** | Small, bounded, frequently accessed | Large, shared, or rarely accessed |

---

### 🔹 Example: Blog Post + Comments

#### ✅ Embed (Good for small blogs)
```json
{
  "title": "My Post",
  "comments": [
    { "author": "Bob", "text": "Great!" },
    { "author": "Alice", "text": "Thanks" }
  ]
}
```

✅ Fast to load post with comments.

#### ✅ Reference (Good for active blogs)
```json
// posts
{ "_id": "p1", "title": "My Post" }

// comments
{ "postId": "p1", "author": "Bob", "text": "Great!" }
```

✅ Scales to millions of comments.

---

### 📌 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Embed when data is small and accessed together** | Faster reads |
| ✅ **Reference when data is large or shared** | Avoid 16MB limit |
| ✅ **Use `$lookup` sparingly** | Can be slow |
| ✅ **Consider hybrid models** | Embed recent comments, reference old ones |

---

### 📌 Interview Answer

> _"Embedding stores related data inside a document — fast but size-limited. Referencing stores data in separate collections — scalable but requires joins. I embed user profiles with preferences, reference orders from users. I choose based on access patterns and data size."_  

---

## 29. How do you secure a MongoDB database?

> Security is **shared responsibility** — you must protect data, access, and network.

---

### 🔹 1. Authentication & Authorization (IAM)

| Method | How |
|------|-----|
| ✅ **SCRAM** | Username/password (default) |
| ✅ **x.509 Certificates** | Mutual TLS authentication |
| ✅ **LDAP / Kerberos** | Enterprise identity integration |
| ✅ **AWS IAM** | For Atlas clusters |

```javascript
// Create user
db.createUser({
  user: "appUser",
  pwd: "securePass123",
  roles: [ { role: "readWrite", db: "myapp" } ]
})
```

---

### 🔹 2. Role-Based Access Control (RBAC)

| Built-in Role | Permissions |
|--------------|------------|
| `read` | Read only |
| `readWrite` | Read and write |
| `dbAdmin` | Database administration |
| `userAdmin` | Manage users/roles |
| `clusterAdmin` | Full cluster access (avoid) |

✅ **Best Practice**: Use **least privilege** — never `root`.

---

### 🔹 3. Network Security

| Measure | How |
|--------|-----|
| ✅ **Firewall / Security Groups** | Allow only trusted IPs |
| ✅ **VPC Peering / Private Endpoints** | Keep traffic within private network |
| ✅ **Disable HTTP Interface** | Turn off MongoDB HTTP interface |
| ✅ **Use TLS/SSL** | Encrypt data in transit |

```yaml
# mongod.conf
net:
  bindIp: 127.0.0.1,10.0.1.100
  port: 27017
  tls:
    mode: requireTLS
    certificateKeyFile: /etc/ssl/mongodb.pem
```

---

### 🔹 4. Encryption at Rest

| Method | How |
|------|-----|
| ✅ **Storage Encryption** | Enable in MongoDB Enterprise or Atlas |
| ✅ **Disk Encryption** | Use LUKS (Linux), BitLocker (Windows) |
| ✅ **TDE (Transparent Data Encryption)** | With enterprise license |

---

### 🔹 5. Auditing & Monitoring

| Tool | Purpose |
|------|--------|
| ✅ **MongoDB Audit Log** | Log authentication, CRUD, admin ops |
| ✅ **Atlas Monitoring** | CPU, memory, queries |
| ✅ **SIEM Integration** | Send logs to Splunk, ELK |

---

### 🔹 6. Best Practices

| Rule | Why |
|------|-----|
| ✅ **Enable Authentication** | Prevent unauthorized access |
| ✅ **Use TLS** | Encrypt in transit |
| ✅ **Patch Regularly** | Fix vulnerabilities |
| ✅ **Disable Unused Features** | Like REST interface |
| ✅ **Use MongoDB Atlas** | Managed service with built-in security |
| ✅ **Regular Backups** | Protect against ransomware |

---

### 📌 Interview Answer

> _"I secure MongoDB with authentication (SCRAM or x.509), role-based access, and network controls (firewall, VPC). I enable TLS for encryption in transit and storage encryption at rest. I audit access and monitor performance. In production, I use MongoDB Atlas for managed security."_  

---

## 30. When would you choose SQL vs NoSQL for a new project?

> The choice depends on **data model**, **scale**, **consistency needs**, and **team expertise**.

---

### ✅ Choose **SQL** when:

| Requirement | Why |
|-----------|-----|
| ✅ **Structured, Relational Data** | Orders, customers, invoices |
| ✅ **Complex Queries & Joins** | Financial reports, analytics |
| ✅ **Strong Consistency** | Banking, inventory |
| ✅ **ACID Transactions** | Order placement, payments |
| ✅ **Mature Ecosystem** | ORM, BI tools, reporting |
| ✅ **Regulatory Compliance** | GDPR, HIPAA (audit, integrity) |

**Examples**:  
- E-commerce backend  
- Banking systems  
- ERP, CRM  

---

### ✅ Choose **NoSQL** when:

| Requirement | Why |
|-----------|-----|
| ✅ **Flexible/Unstructured Data** | JSON, logs, IoT data |
| ✅ **High Write Throughput** | 10K+ writes/sec |
| ✅ **Horizontal Scalability** | Global apps, massive scale |
| ✅ **Low Latency, High Availability** | Real-time apps, mobile |
| ✅ **Rapid Iteration** | MVP, evolving schema |
| ✅ **Specific Workloads** | Caching (Redis), time-series (Cassandra) |

**Examples**:  
- User profiles (MongoDB)  
- Session store (Redis)  
- IoT platform (Cassandra)  
- Content management (Firestore)  

---

### 🔹 Hybrid Approach (Best of Both)

```text
[Web App]
   ↓
[Redis] ← Caching, sessions
   ↓
[PostgreSQL] ← Orders, payments (SQL)
   ↓
[MongoDB] ← User profiles, activity (NoSQL)
```

✅ Use the **right tool for the job**.

---

### 📌 Interview Answer

> _"I choose SQL for structured data with relationships, strong consistency, and complex queries — like e-commerce. I choose NoSQL for flexible schemas, high scale, and real-time apps — like user profiles or IoT. For large systems, I often use both: SQL for transactions, NoSQL for scale."_  

---

## ✅ Final Tip

> 🎯 In interviews, **show balanced thinking**:
> _"NoSQL is powerful, but not a silver bullet. I evaluate based on data model, scale, and consistency. Sometimes SQL is the better choice."_  

That shows **maturity and architectural judgment**.

---

