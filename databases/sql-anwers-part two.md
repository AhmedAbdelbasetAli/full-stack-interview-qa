# 🗄️ Database Deep Dive (Part 2)  
**Constraints, Normalization, Indexes & Denormalization**

This document dives into **advanced database design concepts** — essential for **data modeling**, **performance optimization**, and **system design interviews**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 6. What is a unique constraint?

> A **unique constraint** ensures that **all values in a column (or set of columns) are distinct** — no duplicates allowed.

It enforces **data integrity** at the database level.

---

### 🔹 6.1 Syntax (SQL)

```sql
-- On a single column
ALTER TABLE users ADD CONSTRAINT uk_email UNIQUE (email);

-- On multiple columns (composite unique)
ALTER TABLE user_roles ADD CONSTRAINT uk_user_role UNIQUE (user_id, role_id);
```

✅ Can be defined at:
- Column level
- Table level
- As a named constraint

---

### 🔹 6.2 Example: Prevent Duplicate Emails

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,  -- Prevents duplicate emails
    name VARCHAR(100)
);
```

✅ Inserting duplicate email → `ERROR: duplicate key value violates unique constraint`

---

### 🔹 6.3 Unique Constraint vs Primary Key

| Feature | Unique Constraint | Primary Key |
|--------|-------------------|------------|
| **Null Values** | ✅ One `NULL` allowed (in most DBs) | ❌ Not allowed |
| **Number per Table** | ✅ Multiple | ✅ Only one |
| **Index** | ✅ Automatically indexed | ✅ Indexed (clustered in some DBs) |
| **Purpose** | Enforce uniqueness | Identify rows uniquely |

> 🔁 A **Primary Key** is a **unique constraint that does not allow NULLs**.

---

### 📌 Interview Answer

> _"A unique constraint ensures no duplicate values in a column or combination of columns. I use it to enforce business rules — like unique emails or preventing duplicate user-role assignments. Unlike primary keys, it allows one NULL. It’s enforced by the database and automatically indexed for performance."_  

---

## 7. What is normalization? Why is it important?

> **Normalization** is the process of **organizing data in a database** to **reduce redundancy** and **improve data integrity**.

It involves dividing large tables into smaller, related ones and defining relationships between them.

---

### 🔹 7.1 Goals of Normalization

| Goal | Why It Matters |
|------|---------------|
| ✅ **Eliminate Redundancy** | Same data stored multiple times → waste of space |
| ✅ **Prevent Update Anomalies** | Changing data in one place but not others |
| ✅ **Ensure Data Dependencies Make Sense** | Related data stored together |
| ✅ **Improve Integrity** | Reduce risk of inconsistent data |

---

### 🔹 7.2 Example: Unnormalized Table

```sql
-- Unnormalized: Repeating groups, redundancy
Orders:
| order_id | customer_name | customer_email | product1 | product2 | status |
|----------|---------------|----------------|----------|----------|--------|
| 101      | Alice         | a@x.com        | Laptop   | Mouse    | Shipped|
```

❌ Problems:
- Can't easily add more products
- Customer data repeated
- Hard to query products

---

### ✅ Normalized Design

```sql
Customers:          Orders:               OrderItems:
| id | name  | email     |   | id | customer_id |   | order_id | product_id |
|----|-------|-----------|   |----|-------------|   |----------|------------|
| 1  | Alice | a@x.com   |   | 101| 1           |   | 101      | 201        |
                                 | 102| 2           |   | 101      | 202        |
```

✅ No redundancy, flexible, scalable.

---

### 📌 Interview Answer

> _"Normalization organizes data to reduce redundancy and prevent anomalies. I break large tables into smaller, related ones with foreign keys. It improves data integrity and makes the schema more maintainable. I normalize to 3NF by default unless performance requires denormalization."_  

---

## 8. What are the first three normal forms (1NF, 2NF, 3NF)?

These are **progressive rules** for database normalization.

---

### 🔹 8.1 1NF (First Normal Form)

> **Rules**:
1. Each column contains **atomic (indivisible) values**
2. Each row is **unique**
3. No repeating groups

❌ Bad:
```sql
| order_id | products         |
|----------|------------------|
| 101      | Laptop, Mouse    |  -- Not atomic
```

✅ Good:
```sql
| order_id | product  |
|----------|----------|
| 101      | Laptop   |
| 101      | Mouse    |
```

---

### 🔹 8.2 2NF (Second Normal Form)

> Must be in **1NF** and:
- **All non-key attributes** must be **fully functionally dependent** on the **entire primary key** (not partial)

❌ Bad (Composite PK: order_id + product_id):
```sql
OrderDetails:
| order_id | product_id | order_date | customer_name | quantity |
|----------|------------|------------|---------------|----------|
| 101      | 201        | 2025-04-01 | Alice         | 2        |
```

❌ `order_date` and `customer_name` depend only on `order_id`, not the full key.

✅ Good:
```sql
Orders:                         OrderItems:
| order_id | order_date | customer_name |   | order_id | product_id | quantity |
|----------|------------|---------------|   |----------|------------|----------|
| 101      | 2025-04-01 | Alice         |   | 101      | 201        | 2        |
```

---

### 🔹 8.3 3NF (Third Normal Form)

> Must be in **2NF** and:
- **No transitive dependencies** — non-key attributes must not depend on other non-key attributes

❌ Bad:
```sql
Employees:
| emp_id | name  | dept_id | dept_name | manager |
|--------|-------|---------|-----------|---------|
| 1      | Alice | 10      | IT        | Bob     |
```

❌ `dept_name` and `manager` depend on `dept_id`, not `emp_id`.

✅ Good:
```sql
Employees:                      Departments:
| emp_id | name  | dept_id |   | dept_id | dept_name | manager |
|--------|-------|---------|   |---------|-----------|---------|
| 1      | Alice | 10      |   | 10      | IT        | Bob     |
```

---

### 📌 Interview Answer

> _"1NF: atomic values, no repeating groups. 2NF: full functional dependency on the entire primary key — split partial dependencies. 3NF: no transitive dependencies — move fields that depend on non-key columns to separate tables. I use these to eliminate redundancy and ensure data integrity."_  

---

## 9. What is denormalization? When would you use it?

> **Denormalization** is the **intentional introduction of redundancy** into a database to **improve read performance**.

It’s the **opposite of normalization**.

---

### 🔹 9.1 Why Denormalize?

| Problem | Denormalization Solution |
|--------|--------------------------|
| ❌ Slow JOINs on large tables | Store pre-joined data |
| ❌ Complex queries with many JOINs | Add redundant columns |
| ❌ High-read, low-write workloads | Optimize for reads |

---

### 🔹 9.2 Example: Denormalized Order View

```sql
-- Normalized: Requires JOIN
SELECT o.id, c.name, c.email, p.name, oi.quantity
FROM Orders o
JOIN Customers c ON o.customer_id = c.id
JOIN OrderItems oi ON o.id = oi.order_id
JOIN Products p ON oi.product_id = p.id;

-- Denormalized: Single table
OrderSummary:
| order_id | customer_name | customer_email | product_name | quantity |
|----------|---------------|----------------|--------------|----------|
| 101      | Alice         | a@x.com        | Laptop       | 1        |
```

✅ Faster reads — no JOINs

---

### 🔹 9.3 When to Use Denormalization

| Use Case | Example |
|--------|--------|
| ✅ **Reporting & Analytics** | Data warehouses, OLAP |
| ✅ **High-Read APIs** | Product catalog, user profiles |
| ✅ **Caching Layer** | Materialized views, read replicas |
| ✅ **Eventual Consistency Systems** | DynamoDB, Cassandra |

---

### 🔹 9.4 Trade-Offs

| Pros | Cons |
|------|------|
| ✅ Faster reads | ❌ Data redundancy |
| ✅ Fewer JOINs | ❌ Update anomalies |
| ✅ Simpler queries | ❌ More storage |
| ✅ Better for analytics | ❌ Harder to maintain |

---

### 📌 Interview Answer

> _"Denormalization adds redundancy to improve read performance. I use it in reporting, dashboards, or high-traffic APIs where JOINs are too slow. For example, I store customer name in OrderSummary to avoid JOINs. But I avoid it in transactional systems where data integrity is critical."_  

---

## 10. What is an index? How does it improve query performance?

> An **index** is a **database structure** (like a B-tree or hash) that **speeds up data retrieval** by allowing the database to **find rows quickly** without scanning the entire table.

Think of it as a **book's index** — helps you find pages fast.

---

### 🔹 10.1 How Indexes Work

```sql
-- Without index: Full table scan (slow)
SELECT * FROM users WHERE email = 'alice@example.com';

-- With index: Fast lookup
CREATE INDEX idx_email ON users(email);
```

✅ Index stores `(email → row pointer)` — enables **O(log n)** search.

---

### 🔹 10.2 Types of Indexes

| Type | Use Case |
|------|---------|
| **B-Tree** | Default — good for equality and range queries |
| **Hash Index** | Equality only (e.g., `WHERE id = 10`) |
| **Composite Index** | Multiple columns (`CREATE INDEX idx_name_age ON users(name, age)`) |
| **Unique Index** | Enforces uniqueness + speeds up lookup |
| **Full-Text Index** | Text search (e.g., `MATCH(...) AGAINST(...)`) |

---

### 🔹 10.3 Example: Index Impact

```sql
-- Table: 1M rows
-- Query: SELECT * FROM users WHERE status = 'active';

-- Without index: 1M rows scanned → 500ms
-- With index on `status`: 10K rows → 5ms
```

✅ Speedup: **100x**

---

### 🔹 10.4 When to Use Indexes

| Use Case | Index |
|--------|-------|
| ✅ Frequent `WHERE` clauses | `CREATE INDEX ON users(email)` |
| ✅ `JOIN` columns | `CREATE INDEX ON orders(user_id)` |
| ✅ `ORDER BY` / `GROUP BY` | `CREATE INDEX ON users(created_at)` |
| ✅ Foreign keys | Often indexed automatically |

---

### 🔹 10.5 Pitfalls

| Issue | Solution |
|------|---------|
| ❌ Too many indexes | Slows down INSERT/UPDATE/DELETE |
| ❌ Index on low-cardinality column (e.g., `gender`) | Not effective — use only if highly selective |
| ❌ Not using composite index properly | Order matters: `WHERE name=? AND age=?` → index on `(name, age)` |

---

### 📌 Interview Answer

> _"An index is a data structure that speeds up queries by allowing fast lookups. It works like a book index — instead of scanning the whole table, the database uses the index to find the row. I create indexes on columns used in WHERE, JOIN, ORDER BY. But I avoid over-indexing — it slows down writes. B-tree is most common for range queries."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I normalize to 3NF to reduce redundancy, use unique constraints for data integrity, and add indexes for performance — sometimes denormalizing for high-read APIs."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---



You're mastering **database design** like a pro. 💪
