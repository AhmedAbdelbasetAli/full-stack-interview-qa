# 🗄️ SQL & Database Deep Dive  
**SQL vs NoSQL, JOINs, Keys, and Relational Design**

This document provides a **comprehensive, in-depth explanation** of essential database concepts — critical for **backend interviews**, **data modeling**, and **system design**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Practical examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 1. What is the difference between SQL and NoSQL databases?

| Feature | SQL (Relational) | NoSQL (Non-Relational) |
|--------|------------------|-----------------------|
| **Data Model** | Tables with rows and columns | Documents, Key-Value, Graph, Column-family |
| **Schema** | Fixed (schema-on-write) | Dynamic (schema-on-read) |
| **Scalability** | Vertical (scale up) | Horizontal (scale out) |
| **ACID Compliance** | ✅ Strong (transactions) | ⚠️ Varies (eventual consistency) |
| **Examples** | MySQL, PostgreSQL, Oracle | MongoDB, Redis, Cassandra, Neo4j |
| **Use Case** | Structured data, complex queries | Unstructured data, high scalability |

---

### 🔹 1.1 SQL Databases – Structured & Consistent

> **Relational databases** that use **SQL (Structured Query Language)**.

✅ Use **tables**, **foreign keys**, and **JOINs**  
✅ Enforce **ACID** (Atomicity, Consistency, Isolation, Durability)  
✅ Ideal for:  
- Financial systems  
- ERP, CRM  
- Apps requiring complex queries and transactions

```sql
SELECT u.name, o.total 
FROM users u 
JOIN orders o ON u.id = o.user_id;
```

---

### 🔹 1.2 NoSQL Databases – Flexible & Scalable

> **Non-relational databases** for **flexible schemas** and **horizontal scaling**.

#### 🟦 Types of NoSQL:
| Type | Use Case | Example |
|------|--------|--------|
| **Document** | JSON-like data | MongoDB |
| **Key-Value** | Caching, sessions | Redis |
| **Column-Family** | Big data, analytics | Cassandra |
| **Graph** | Relationships, networks | Neo4j |

✅ Ideal for:  
- Real-time apps  
- IoT, logs  
- Rapidly evolving schemas

```json
// MongoDB document
{
  "name": "Alice",
  "email": "alice@example.com",
  "orders": [ { "id": 101, "total": 99.99 } ]
}
```

---

### 📌 Interview Answer

> _"SQL databases are relational, use fixed schemas, and support ACID transactions — ideal for structured data like banking. NoSQL databases are non-relational, schema-flexible, and scale horizontally — great for unstructured data like logs or user profiles. I choose SQL for consistency and complex queries, NoSQL for scalability and flexibility."_  

---

## 2. What is a relational database?

> A **relational database** stores data in **tables (relations)** with **rows (tuples)** and **columns (attributes)**, and defines **relationships** between tables using **keys**.

It follows **relational algebra** and **Codd’s 12 rules**.

---

### 🔹 2.1 Key Concepts

| Term | Meaning |
|------|--------|
| **Table** | Collection of related data (e.g., `users`, `orders`) |
| **Row** | A single record |
| **Column** | Attribute of the data (e.g., `name`, `email`) |
| **Primary Key** | Unique identifier for a row |
| **Foreign Key** | Links to primary key in another table |
| **Constraint** | Rules (NOT NULL, UNIQUE, CHECK) |
| **Index** | Speeds up queries on columns |

---

### 🔹 2.2 Example: Users and Orders

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE
);

CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    total DECIMAL(10,2),
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

✅ Relationship: **One user → Many orders** (One-to-Many)

---

### 🔹 2.3 ACID Properties

| Property | Meaning |
|--------|--------|
| **Atomicity** | All or nothing |
| **Consistency** | Data follows rules |
| **Isolation** | Concurrent transactions don’t interfere |
| **Durability** | Committed data survives crashes |

---

### 📌 Interview Answer

> _"A relational database organizes data into tables with rows and columns. Relationships are defined using foreign keys. It enforces data integrity through constraints and supports ACID transactions. I use it when data structure is stable and consistency is critical — like in banking or inventory systems."_  

---

## 3. What are the different types of SQL JOINs? Explain each.

JOINs combine rows from two or more tables based on a related column.

---

### 🔹 3.1 `INNER JOIN` – Matching Rows Only

Returns only rows where there’s a match in **both tables**.

```sql
SELECT u.name, o.total
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```

✅ Result: Only users **with orders** and orders **with valid users**

---

### 🔹 3.2 `LEFT JOIN` (or `LEFT OUTER JOIN`) – All from Left

Returns **all rows from the left table**, and matched rows from the right. Unmatched right values → `NULL`.

```sql
SELECT u.name, o.total
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```

✅ Result: All users — even those **without orders**

---

### 🔹 3.3 `RIGHT JOIN` (or `RIGHT OUTER JOIN`) – All from Right

Returns **all rows from the right table**, and matched rows from the left.

```sql
SELECT u.name, o.total
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

✅ Result: All orders — even if user is missing (e.g., deleted user)

---

### 🔹 3.4 `FULL OUTER JOIN` – All Rows

Returns **all rows from both tables**. Unmatched sides → `NULL`.

```sql
SELECT u.name, o.total
FROM users u
FULL OUTER JOIN orders o ON u.id = o.user_id;
```

✅ Result: All users and all orders — even unmatched ones

> ⚠️ Not supported in MySQL (use `UNION` of `LEFT` and `RIGHT`)

---

### 🔹 3.5 `CROSS JOIN` – Cartesian Product

Returns **every combination** of rows from both tables.

```sql
SELECT u.name, p.product_name
FROM users u
CROSS JOIN products p;
```

✅ Result: `n × m` rows — use carefully!

---

### 📌 Interview Answer

> _"The main JOINs are: `INNER JOIN` (matching rows), `LEFT JOIN` (all left + matched right), `RIGHT JOIN` (all right + matched left), `FULL OUTER JOIN` (all rows), and `CROSS JOIN` (all combinations). I use `INNER JOIN` for strict relationships, `LEFT JOIN` to include all users even without orders, and avoid `CROSS JOIN` unless intentional."_  

---

## 4. What is the difference between `INNER JOIN` and `LEFT JOIN`?

| Feature | `INNER JOIN` | `LEFT JOIN` |
|--------|--------------|-------------|
| **Rows Returned** | Only matching rows | All from left + matched from right |
| **Unmatched Rows** | Excluded | Included with `NULL` on right |
| **Use Case** | Strict relationships | Optional relationships |
| **Example** | Users with orders | All users (with or without orders) |

---

### 🔹 4.1 Example

**Users Table**
| id | name   |
|----|--------|
| 1  | Alice  |
| 2  | Bob    |
| 3  | Charlie|

**Orders Table**
| id | user_id | total |
|----|---------|-------|
| 101| 1       | 99.99 |
| 102| 1       | 49.99 |

#### 🟦 `INNER JOIN`
```sql
SELECT u.name, o.total FROM users u INNER JOIN orders o ON u.id = o.user_id;
```
✅ Result:  
- Alice → 99.99  
- Alice → 49.99  
❌ Bob and Charlie excluded

#### 🟦 `LEFT JOIN`
```sql
SELECT u.name, o.total FROM users u LEFT JOIN orders o ON u.id = o.user_id;
```
✅ Result:  
- Alice → 99.99  
- Alice → 49.99  
- Bob → NULL  
- Charlie → NULL

---

### 📌 Interview Answer

> _"`INNER JOIN` returns only rows with matches in both tables — like users who have placed orders. `LEFT JOIN` returns all rows from the left table, even if there's no match — like all users, including those without orders. I use `LEFT JOIN` when I want to include 'zero' cases in reports or analytics."_  

---

## 5. What is a primary key? How is it different from a foreign key?

| Feature | Primary Key | Foreign Key |
|--------|-------------|------------|
| **Purpose** | Uniquely identify a row | Link to another table's primary key |
| **Uniqueness** | ✅ Yes (unique) | ❌ No (can repeat) |
| **Null Values** | ❌ No (`NOT NULL`) | ✅ Yes (unless constrained) |
| **Number per Table** | ✅ One (but can be composite) | ✅ Multiple |
| **Index** | ✅ Automatically indexed | ✅ Should be indexed for performance |

---

### 🔹 5.1 Primary Key – Unique Identifier

```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(100) UNIQUE
);
```

✅ Rules:
- Must be **unique**
- Cannot be `NULL`
- One per table
- Often `id` (surrogate key)

---

### 🔹 5.2 Foreign Key – Relationship Enforcer

```sql
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    total DECIMAL(10,2),
    FOREIGN KEY (user_id) REFERENCES users(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

✅ Rules:
- References a **primary key** in another table
- Ensures **referential integrity**
- Can be `NULL` (unless `NOT NULL`)
- Can repeat (e.g., same user has multiple orders)

> 🔗 Enforces: "You can't create an order for a non-existent user"

---

### 🔹 5.3 Composite Primary Key

```sql
CREATE TABLE user_roles (
    user_id INT,
    role_id INT,
    PRIMARY KEY (user_id, role_id)
);
```

✅ When a single column isn’t enough to guarantee uniqueness.

---

### 📌 Interview Answer

> _"A primary key uniquely identifies each row in a table — it's unique and not null. A foreign key links to a primary key in another table, enforcing referential integrity. For example, `orders.user_id` is a foreign key referencing `users.id`. The primary key ensures no duplicate users; the foreign key ensures every order belongs to a valid user."_  

---

## ✅ Final Tip

> 🎯 In interviews, **draw the schema** when explaining JOINs or keys — it makes your answer **clearer and more impressive**.

---



You're mastering databases like a pro. 💪
