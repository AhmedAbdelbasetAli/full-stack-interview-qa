# 🗄️ Advanced Database Concepts  
**Index Disadvantages, Composite Index, Transactions, ACID & DELETE vs TRUNCATE vs DROP**

This document covers **essential advanced database concepts** — critical for **performance tuning**, **data integrity**, and **system design interviews**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 11. What are the disadvantages of using indexes?

> While indexes **speed up reads**, they come with **costs** that can **hurt performance** if overused.

---

### 🔹 11.1 Key Disadvantages

| Disadvantage | Explanation |
|-------------|-------------|
| ❌ **Slower Writes** | Every `INSERT`, `UPDATE`, `DELETE` must update the index → slower DML operations |
| ❌ **Increased Storage** | Indexes take up disk space — sometimes as much as the table itself |
| ❌ **Index Maintenance Overhead** | The database must keep indexes synchronized with data |
| ❌ **Too Many Indexes = Diminishing Returns** | Each additional index has less benefit but same write cost |
| ❌ **Can Be Ignored by Optimizer** | Poorly designed or unused indexes are wasted |
| ❌ **Fragmentation** | Over time, indexes become fragmented → slower scans |
| ❌ **Not Always Used** | Query must use indexed column in `WHERE`, `JOIN`, `ORDER BY` |

---

### 🔹 11.2 Example: Write Performance Impact

```sql
-- Table: 1M rows, 5 indexes
INSERT INTO users (name, email) VALUES ('Alice', 'a@x.com');
```

✅ Without indexes: Fast insert  
❌ With 5 indexes: Database must update **6 structures** (table + 5 indexes)

---

### 🔹 11.3 When Indexes Are Harmful

| Scenario | Why |
|--------|-----|
| ❌ **High-write, low-read tables** | Logging, audit tables |
| ❌ **Small tables** | Full scan is faster than index lookup |
| ❌ **Columns with low cardinality** | `gender` (M/F) — index not selective |
| ❌ **Unused indexes** | No queries use them → pure overhead |

---

### 📌 Interview Answer

> _"Indexes speed up reads but slow down writes and consume storage. Each INSERT/UPDATE/DELETE must update the index. Too many indexes hurt write performance and waste space. I only index columns used in WHERE, JOIN, or ORDER BY, and remove unused ones. For high-write tables, I avoid over-indexing."_  

---

## 12. What is a composite index?

> A **composite index** (or **compound index**) is an index on **two or more columns**.

It’s used when queries **filter or sort on multiple columns together**.

---

### 🔹 12.1 Syntax (SQL)

```sql
CREATE INDEX idx_name_age ON users (last_name, first_name, age);
```

✅ Index is sorted by: `last_name` → `first_name` → `age`

---

### 🔹 12.2 How It Works

```sql
-- This query can use the composite index
SELECT * FROM users 
WHERE last_name = 'Smith' 
  AND first_name = 'John';

-- This might use it (depends on optimizer)
SELECT * FROM users 
WHERE last_name = 'Smith' 
ORDER BY first_name;
```

❌ But this **cannot use it efficiently**:
```sql
SELECT * FROM users WHERE first_name = 'John';
```

✅ **Column order matters**: Only leftmost prefixes are used.

---

### 🔹 12.3 Index Prefix Matching

| Query Condition | Can Use Index? |
|----------------|----------------|
| `WHERE last_name = 'A'` | ✅ Yes (prefix) |
| `WHERE last_name = 'A' AND first_name = 'B'` | ✅ Yes |
| `WHERE last_name = 'A' AND first_name = 'B' AND age = 25` | ✅ Yes |
| `WHERE first_name = 'B'` | ❌ No |
| `WHERE age = 25` | ❌ No |

---

### 🔹 12.4 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Put most selective column first** | Filters more rows early |
| ✅ **Match query patterns** | Index columns used together |
| ✅ **Limit to 3–5 columns** | Too many → slow updates, large size |
| ✅ **Use for foreign key + status** | `user_id` + `status` |

---

### 📌 Interview Answer

> _"A composite index is on multiple columns, like `(last_name, first_name)`. It’s efficient for queries that filter on both. Order matters — only leftmost prefixes are used. I use it for common multi-column filters, like user ID and status. But I avoid too many columns to keep it fast."_  

---

## 13. What is a transaction in SQL?

> A **transaction** is a **single logical unit of work** that consists of **one or more SQL statements**.

It’s **all-or-nothing**: either all statements succeed, or none do.

---

### 🔹 13.1 Example: Bank Transfer

```sql
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE user_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE user_id = 2;

COMMIT; -- Both succeed
-- or
ROLLBACK; -- Both fail if any error
```

✅ Ensures money isn’t lost if system crashes mid-transfer.

---

### 🔹 13.2 Transaction Control Commands

| Command | Purpose |
|--------|--------|
| `BEGIN` / `START TRANSACTION` | Start a transaction |
| `COMMIT` | Save all changes permanently |
| `ROLLBACK` | Undo all changes since `BEGIN` |
| `SAVEPOINT` | Set a point to rollback to (partial rollback) |

---

### 🔹 13.3 Auto-Commit Mode

- Most databases default to **auto-commit = ON**
- Each statement is its own transaction
- Turn off with: `SET autocommit = 0;`

---

### 📌 Interview Answer

> _"A transaction is a group of SQL operations that must all succeed or all fail. I use it for critical operations like money transfers. I wrap related UPDATEs in a BEGIN-COMMIT block. If any fails, I ROLLBACK to keep data consistent. This ensures atomicity."_  

---

## 14. What are ACID properties in a transaction?

> **ACID** is a set of **guarantees** that ensure **database transactions are processed reliably**.

| Property | Meaning |
|--------|--------|
| ✅ **A: Atomicity** | All operations in a transaction are treated as one unit — all succeed or all fail |
| ✅ **C: Consistency** | Transaction brings the database from one valid state to another |
| ✅ **I: Isolation** | Concurrent transactions don’t interfere with each other |
| ✅ **D: Durability** | Once committed, changes survive crashes (written to disk) |

---

### 🔹 14.1 Deep Dive

#### ✅ Atomicity
- Ensured by **rollback logs**
- If half the transaction fails, all changes are undone

#### ✅ Consistency
- Enforced by **constraints** (PK, FK, unique, check)
- No transaction can violate rules

#### ✅ Isolation
- Controlled by **isolation levels**:
  - Read Uncommitted
  - Read Committed (default)
  - Repeatable Read
  - Serializable
- Prevents dirty reads, non-repeatable reads, phantom reads

#### ✅ Durability
- Changes written to **transaction log** and then data files
- Even if power fails after `COMMIT`, changes are recoverable

---

### 🔹 14.2 Example: ACID in Action

```sql
BEGIN;

-- Atomicity: Both must succeed
UPDATE accounts SET balance = balance - 50 WHERE id = 1;
UPDATE accounts SET balance = balance + 50 WHERE id = 2;

-- Consistency: Balance can't go negative (check constraint)
-- Isolation: Other transactions see either both updates or none
-- Durability: After COMMIT, changes are safe

COMMIT;
```

---

### 📌 Interview Answer

> _"ACID ensures reliable transactions: Atomicity (all or nothing), Consistency (valid state), Isolation (no interference), Durability (survives crashes). I rely on it for financial systems. Databases use logs and locks to enforce these. Without ACID, data could be corrupted during failures."_  

---

## 15. What is the difference between DELETE, TRUNCATE, and DROP?

| Feature | `DELETE` | `TRUNCATE` | `DROP` |
|--------|---------|-----------|--------|
| **Purpose** | Remove rows | Remove all rows | Remove entire table |
| **DML / DDL** | DML | DDL | DDL |
| **WHERE Clause** | ✅ Yes | ❌ No | N/A |
| **Speed** | Slower (row-by-row) | Fast (deallocates pages) | Fast |
| **Rollback** | ✅ Yes (in transaction) | ❌ Usually not | ❌ No |
| **Triggers** | ✅ Fired | ❌ Not fired | ❌ Not fired |
| **Auto-increment Reset** | ❌ No | ✅ Yes (in most DBs) | ✅ Yes |
| **Referential Integrity** | ✅ Respected | ❌ May bypass | ❌ Drops constraints |
| **Table Structure** | ✅ Kept | ✅ Kept | ❌ Removed |

---

### 🔹 15.1 When to Use Which?

| Use Case | Command |
|--------|--------|
| ✅ **Remove specific rows** | `DELETE FROM users WHERE status = 'inactive';` |
| ✅ **Clear all data fast** | `TRUNCATE TABLE logs;` |
| ✅ **Remove table and data** | `DROP TABLE temp_data;` |

---

### 🔹 15.2 Examples

```sql
-- DELETE: Specific rows, can rollback
DELETE FROM users WHERE created_at < '2020-01-01';

-- TRUNCATE: All rows, fast, resets auto-increment
TRUNCATE TABLE audit_log;

-- DROP: Table gone forever
DROP TABLE old_table;
```

---

### 📌 Interview Answer

> _"DELETE removes specific rows, supports WHERE, and can be rolled back. TRUNCATE removes all rows fast, resets auto-increment, but can't be rolled back in many systems. DROP removes the entire table. I use DELETE for selective removal, TRUNCATE for clearing logs, and DROP only when the table is no longer needed."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use composite indexes for multi-column queries, but avoid over-indexing. I wrap critical operations in ACID transactions to ensure consistency."_  

That shows **deep, integrated understanding** — exactly what senior roles want.

---
