# 🗄️ SQL Deep Dive (Part 6)  
**LIKE, Duplicates, DISTINCT & LIMIT**

This document covers **practical SQL operations** — essential for **data cleaning**, **search**, and **result control**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 26. What is the LIKE operator used for? Give examples.

> The `LIKE` operator is used for **pattern matching** in strings — with **wildcards**.

It’s essential for **fuzzy text search**.

---

### 🔹 26.1 Wildcards

| Wildcard | Meaning | Example |
|--------|--------|--------|
| `%` | Matches **zero or more** characters | `'A%'` → "Alice", "Apple" |
| `_` | Matches **exactly one** character | `'A__e'` → "Alex", "Able" |

---

### 🔹 26.2 Examples

```sql
-- Table: users
| name     | email           |
|----------|-----------------|
| Alice    | alice@x.com     |
| Bob      | bob@y.org       |
| Alice2   | alice2@z.net    |
| Charlie  | charlie@x.com   |
```

#### ✅ Find names starting with 'A'
```sql
SELECT * FROM users WHERE name LIKE 'A%';
-- Result: Alice, Alice2
```

#### ✅ Find emails ending with '@x.com'
```sql
SELECT * FROM users WHERE email LIKE '%@x.com';
-- Result: alice@x.com, charlie@x.com
```

#### ✅ Find names with 'ob' anywhere
```sql
SELECT * FROM users WHERE name LIKE '%ob%';
-- Result: Bob
```

#### ✅ Find names with exactly 4 letters, ending with 'e'
```sql
SELECT * FROM users WHERE name LIKE '___e';
-- Result: Alice (no), Bob (no), Alice2 (no) → none match
-- But 'Able' would match
```

---

### 🔹 26.3 Case Sensitivity

| Database | Case-Sensitive? |
|--------|-----------------|
| **MySQL** | Depends on collation (usually case-insensitive) |
| **PostgreSQL** | Case-sensitive (`ILIKE` for case-insensitive) |
| **SQL Server** | Depends on collation |
| **SQLite** | Case-insensitive for ASCII |

✅ For case-insensitive search:
```sql
-- PostgreSQL
SELECT * FROM users WHERE name ILIKE 'alice';

-- All databases
SELECT * FROM users WHERE UPPER(name) = 'ALICE';
```

---

### 📌 Interview Answer

> _"LIKE is for pattern matching with % (any chars) and _ (one char). I use it to find names starting with 'A' (LIKE 'A%') or emails from a domain (LIKE '%@gmail.com'). I'm careful with performance — LIKE with leading % can't use indexes efficiently. For case-insensitive search, I use ILIKE (PostgreSQL) or UPPER()."_  

---

## 27. How do you find duplicate rows in a table?

> Find rows that have **identical values** in key columns.

Use `GROUP BY` + `HAVING COUNT(*) > 1`.

---

### 🔹 27.1 Example: Find duplicate emails

```sql
-- Table: users
| id | name  | email         |
|----|-------|---------------|
| 1  | Alice | alice@x.com   |
| 2  | Bob   | bob@y.com     |
| 3  | Alice | alice@x.com   | ← Duplicate
| 4  | Charlie | charlie@z.com |
```

#### ✅ Find duplicate email entries
```sql
SELECT email, COUNT(*) as count
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

✅ Result:
```
email        | count
alice@x.com  | 2
```

---

### 🔹 27.2 Find full duplicate rows (all columns)
```sql
SELECT name, email, COUNT(*)
FROM users
GROUP BY name, email
HAVING COUNT(*) > 1;
```

---

### 🔹 27.3 Include IDs of duplicates (for deletion)
```sql
SELECT id, email
FROM users
WHERE email IN (
    SELECT email
    FROM users
    GROUP BY email
    HAVING COUNT(*) > 1
)
ORDER BY email;
```

✅ Result:
```
id | email
1  | alice@x.com
3  | alice@x.com
```

---

### 📌 Interview Answer

> _"I use GROUP BY on the columns that define duplicates (like email), then HAVING COUNT(*) > 1 to find groups with more than one row. This is efficient and clear. I can include IDs if I need to delete them later."_  

---

## 28. How do you delete duplicate rows from a table?

> Keep **one copy** of each duplicate, remove the rest.

---

### 🔹 28.1 Method 1: Using ROW_NUMBER() (Best for most databases)

```sql
-- Delete duplicates, keep the one with smallest ID
DELETE FROM users
WHERE id NOT IN (
    SELECT MIN(id)
    FROM users
    GROUP BY email
);
```

✅ Or with CTE (cleaner):
```sql
WITH CTE AS (
    SELECT id,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) as rn
    FROM users
)
DELETE FROM users
WHERE id IN (
    SELECT id FROM CTE WHERE rn > 1
);
```

---

### 🔹 28.2 Method 2: Self-Join (MySQL, older systems)

```sql
DELETE u1 FROM users u1
INNER JOIN users u2
WHERE u1.id > u2.id AND u1.email = u2.email;
```

✅ Keeps the row with smaller `id`.

---

### 🔹 28.3 Method 3: Using Temporary Table

```sql
-- Create temp table with unique rows
CREATE TABLE temp_users AS
SELECT MIN(id) as id, name, email
FROM users
GROUP BY email;

-- Clear original
TRUNCATE TABLE users;

-- Insert back
INSERT INTO users SELECT * FROM temp_users;
```

✅ Safe but requires more space.

---

### 📌 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Always backup first** | Deletion is irreversible |
| ✅ **Keep the oldest or smallest ID** | Most stable choice |
| ✅ **Test with SELECT before DELETE** | `SELECT * FROM ... WHERE ...` |
| ✅ **Use transactions** | `BEGIN; DELETE ...; ROLLBACK;` to test |

---

### 📌 Interview Answer

> _"I delete duplicates using ROW_NUMBER() over PARTITION BY duplicate key (like email), ordered by ID. I keep rn=1, delete rn>1. It's clean and works in PostgreSQL, SQL Server, Oracle. In MySQL, I might use self-join. I always test with SELECT first and use transactions."_  

---

## 29. What is the DISTINCT keyword?

> `DISTINCT` removes **duplicate rows** from the result set.

It returns only **unique combinations** of selected columns.

---

### 🔹 29.1 Syntax
```sql
SELECT DISTINCT column1, column2
FROM table;
```

---

### 🔹 29.2 Examples

```sql
-- Table: orders
| customer_id | product   | status     |
|-------------|-----------|------------|
| 101         | Laptop    | COMPLETED  |
| 101         | Mouse     | COMPLETED  |
| 102         | Laptop    | PENDING    |
| 101         | Laptop    | COMPLETED  | ← Duplicate
```

#### ✅ Unique customers
```sql
SELECT DISTINCT customer_id FROM orders;
-- Result: 101, 102
```

#### ✅ Unique product-status pairs
```sql
SELECT DISTINCT product, status FROM orders;
-- Result: (Laptop, COMPLETED), (Mouse, COMPLETED), (Laptop, PENDING)
```

---

### 🔹 29.3 DISTINCT vs GROUP BY

```sql
-- These are equivalent:
SELECT DISTINCT product FROM orders;
SELECT product FROM orders GROUP BY product;
```

✅ Use `DISTINCT` for simple deduplication  
✅ Use `GROUP BY` when you need aggregates (`COUNT`, `SUM`)

---

### 🔹 29.4 Performance

| Tip | Why |
|-----|-----|
| ✅ **Index the columns in DISTINCT** | Faster sorting and deduplication |
| ✅ **Avoid DISTINCT on large text fields** | Slow and memory-intensive |
| ✅ **DISTINCT is expensive** | Requires sorting or hashing |

---

### 📌 Interview Answer

> _"DISTINCT removes duplicate rows from the result. I use it to get unique values — like distinct customer IDs or product categories. It's equivalent to GROUP BY without aggregates. But it can be slow on large datasets, so I index the columns and avoid it on text fields when possible."_  

---

## 30. What is the LIMIT (or TOP / ROWNUM) clause used for?

> `LIMIT` (MySQL, PostgreSQL), `TOP` (SQL Server), or `ROWNUM` (Oracle) **restricts the number of rows returned**.

It’s essential for **pagination**, **sampling**, and **performance**.

---

### 🔹 30.1 Syntax by Database

| Database | Syntax |
|--------|--------|
| **MySQL / PostgreSQL** | `LIMIT n` or `LIMIT n OFFSET m` |
| **SQL Server** | `SELECT TOP n * FROM table` |
| **Oracle** | `WHERE ROWNUM <= n` (or `OFFSET ... FETCH` in 12c+) |

---

### 🔹 30.2 Examples

#### ✅ Get top 5 highest-paid employees
```sql
-- MySQL / PostgreSQL
SELECT name, salary
FROM employees
ORDER BY salary DESC
LIMIT 5;

-- SQL Server
SELECT TOP 5 name, salary
FROM employees
ORDER BY salary DESC;

-- Oracle (pre-12c)
SELECT * FROM (
    SELECT name, salary
    FROM employees
    ORDER BY salary DESC
) WHERE ROWNUM <= 5;
```

#### ✅ Pagination: Page 2, 10 items per page
```sql
-- MySQL / PostgreSQL
SELECT * FROM products
ORDER BY name
LIMIT 10 OFFSET 10;  -- Skip first 10, take next 10

-- SQL Server 2012+
SELECT * FROM products
ORDER BY name
OFFSET 10 ROWS
FETCH NEXT 10 ROWS ONLY;
```

---

### 🔹 30.3 Key Rules

| Rule | Why |
|------|-----|
| ✅ **Always use ORDER BY with LIMIT** | Otherwise, which rows are returned is undefined |
| ✅ **OFFSET can be slow** | Database still scans first N rows |
| ✅ **Use keyset pagination for large data** | `WHERE id > last_seen_id` instead of `OFFSET` |

---

### 📌 Interview Answer

> _"LIMIT, TOP, or ROWNUM limits the number of rows returned. I use it for pagination, top-N queries, and sampling. I always pair it with ORDER BY to ensure consistent results. For deep pagination, I prefer keyset pagination (cursor-based) over OFFSET for better performance."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use DISTINCT to deduplicate, LIKE for search, and LIMIT for pagination — always with ORDER BY for consistency."_  

That shows **practical, production-ready SQL skills**.

---
