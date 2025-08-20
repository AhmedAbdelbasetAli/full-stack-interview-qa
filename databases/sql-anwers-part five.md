# 🗄️ SQL Deep Dive (Part 5)  
**Views, UNION, Stored Procedures, Triggers & NULL Handling**

This document covers **advanced SQL concepts** — essential for **database design**, **performance**, and **system architecture**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Best practices
- ✅ Interview-ready answers

---

## 21. What is a view in SQL? Why use it?

> A **view** is a **virtual table** based on the result of a `SELECT` query.

It doesn’t store data — it stores the **query definition**.

---

### 🔹 21.1 Syntax
```sql
CREATE VIEW active_users AS
SELECT id, name, email, created_at
FROM users
WHERE status = 'ACTIVE';
```

✅ Now you can query it like a table:
```sql
SELECT * FROM active_users;
```

---

### 🔹 21.2 Why Use Views?

| Benefit | How It Helps |
|--------|-------------|
| ✅ **Simplify Complex Queries** | Hide joins, filters, calculations |
| ✅ **Security / Access Control** | Expose only needed columns (e.g., hide `salary`) |
| ✅ **Logical Data Independence** | Change underlying schema, keep view same |
| ✅ **Reusable Logic** | Avoid rewriting the same query |
| ✅ **Aggregate Summaries** | Pre-define reports (e.g., `monthly_sales`) |

---

### 🔹 21.3 Example: Secure HR View

```sql
-- Base table: employees (id, name, salary, department, ssn)
-- Risky to give direct access

-- Safe view
CREATE VIEW employee_directory AS
SELECT id, name, department
FROM employees;

-- HR can query this — no salary or SSN exposed
SELECT * FROM employee_directory;
```

---

### 🔹 21.4 Types of Views

| Type | Description |
|------|-------------|
| **Simple View** | Single table, no aggregates |
| **Complex View** | Joins, GROUP BY, functions |
| **Materialized View** | Stores result physically (faster, but needs refresh) — in PostgreSQL, Oracle, Snowflake |

---

### 📌 Interview Answer

> _"A view is a saved query that acts like a virtual table. I use it to simplify complex queries, enforce security by hiding sensitive columns, and provide reusable logic. It decouples applications from schema changes. I avoid overusing materialized views unless performance justifies the storage cost."_  

---

## 22. What is the UNION operator? How is it different from UNION ALL?

> `UNION` combines results from **two or more SELECT statements**.

---

### 🔹 22.1 `UNION` vs `UNION ALL`

| Feature | `UNION` | `UNION ALL` |
|--------|--------|------------|
| **Duplicates** | ❌ Removes duplicates | ✅ Keeps duplicates |
| **Performance** | Slower (sort + dedup) | Faster |
| **Use Case** | Unique combined results | All records, including duplicates |
| **Columns** | Must have same number and compatible types | Same |
| **Order** | Not guaranteed unless `ORDER BY` | Not guaranteed |

---

### 🔹 22.2 Example: Customer and Employee Emails

```sql
-- Table: customers
| name  | email           |
|-------|-----------------|
| Alice | alice@c.com     |

-- Table: employees
| name  | email           |
|-------|-----------------|
| Bob   | bob@e.com       |
| Alice | alice@c.com     | -- Duplicate

-- UNION: Unique emails
SELECT email FROM customers
UNION
SELECT email FROM employees;

-- Result: alice@c.com, bob@e.com (1 row)

-- UNION ALL: All emails
SELECT email FROM customers
UNION ALL
SELECT email FROM employees;

-- Result: alice@c.com, alice@c.com, bob@e.com (3 rows)
```

---

### 🔹 22.3 Rules

| Rule | Why |
|------|-----|
| ✅ **Same number of columns** | Can't combine `SELECT name` with `SELECT name, email` |
| ✅ **Compatible data types** | String with string, number with number |
| ✅ **Use `ORDER BY` at end** | `SELECT ... UNION SELECT ... ORDER BY col` |

---

### 📌 Interview Answer

> _"UNION combines results and removes duplicates — it's slower due to sorting. UNION ALL keeps all rows, including duplicates, and is faster. I use UNION when I need unique results, and UNION ALL when I want all data — like combining logs from multiple sources. I always ensure column count and types match."_  

---

## 23. What is a stored procedure? How is it different from a function?

| Feature | Stored Procedure | Function |
|--------|------------------|---------|
| **Purpose** | Perform actions (DML, logic) | Compute and return a value |
| **Return Value** | Optional (via `OUT` params) | Must return a value |
| **Can Return Multiple Values** | ✅ Yes (OUT params) | ❌ No (single return) |
| **Can be Used in SELECT** | ❌ No | ✅ Yes (scalar functions) |
| **Can Modify Data** | ✅ Yes (`INSERT`, `UPDATE`) | ❌ Usually not (side effects) |
| **Transactions** | Can control (`BEGIN`, `COMMIT`) | Auto-contained |
| **Performance** | Can be faster for complex logic | Good for reuse in queries |

---

### 🔹 23.1 Example: Stored Procedure (MySQL)

```sql
DELIMITER //
CREATE PROCEDURE TransferFunds(
    IN from_account INT,
    IN to_account INT,
    IN amount DECIMAL(10,2)
)
BEGIN
    START TRANSACTION;
    
    UPDATE accounts SET balance = balance - amount WHERE id = from_account;
    UPDATE accounts SET balance = balance + amount WHERE id = to_account;
    
    COMMIT;
END //
DELIMITER ;

-- Call it
CALL TransferFunds(101, 102, 500);
```

---

### 🔹 23.2 Example: Function (MySQL)

```sql
DELIMITER //
CREATE FUNCTION CalculateTax(income DECIMAL(10,2))
RETURNS DECIMAL(10,2)
READS SQL DATA
DETERMINISTIC
BEGIN
    DECLARE tax DECIMAL(10,2);
    SET tax = income * 0.2;
    RETURN tax;
END //
DELIMITER ;

-- Use in SELECT
SELECT name, salary, CalculateTax(salary) as tax_due FROM employees;
```

---

### 📌 Interview Answer

> _"A stored procedure performs actions like updates and supports transactions. It can have OUT parameters but can't be used in SELECT. A function returns a single value and can be used in queries. I use procedures for complex business logic (e.g., fund transfer), and functions for calculations (e.g., tax, discounts)."_  

---

## 24. What is a trigger in SQL?

> A **trigger** is a **stored procedure that automatically executes** in response to **events** like `INSERT`, `UPDATE`, `DELETE`.

It’s like a **database-level callback**.

---

### 🔹 24.1 When Triggers Fire

| Timing | Event |
|-------|------|
| `BEFORE` | Before the DML operation |
| `AFTER` | After the DML operation |
| `INSTEAD OF` | Replace the operation (mainly for views) |

---

### 🔹 24.2 Example: Audit Log Trigger

```sql
-- Table: employees_audit
| employee_id | action | changed_by | changed_at |

-- Trigger: Log updates
CREATE TRIGGER log_employee_update
AFTER UPDATE ON employees
FOR EACH ROW
BEGIN
    INSERT INTO employees_audit (employee_id, action, changed_by, changed_at)
    VALUES (OLD.id, 'UPDATE', USER(), NOW());
END;
```

✅ Every `UPDATE` on `employees` → automatic log entry.

---

### 🔹 24.3 Common Use Cases

| Use Case | Example |
|--------|--------|
| ✅ **Audit Logging** | Track who changed what and when |
| ✅ **Data Validation** | Enforce complex business rules |
| ✅ **Derived Data** | Update `last_modified` timestamp |
| ✅ **Replication / Sync** | Push changes to another system |
| ✅ **Enforce Constraints** | Cross-table rules not possible with FK |

---

### 🔹 24.4 Caveats

| Risk | Why |
|------|-----|
| ❌ **Hidden Logic** | Hard to trace — not in application code |
| ❌ **Performance** | Can slow down DML operations |
| ❌ **Debugging** | Hard to test and debug |
| ❌ **Overuse** | Can make system unpredictable |

✅ **Best Practice**: Use sparingly. Prefer application logic when possible.

---

### 📌 Interview Answer

> _"A trigger is a database object that runs automatically on INSERT, UPDATE, or DELETE. I use it for audit logging, enforcing complex constraints, or updating derived fields. But I avoid overusing it — triggers are hard to debug and can hurt performance. I prefer application-level logic when possible."_  

---

## 25. What is the NULL value? How is it handled in comparisons?

> `NULL` represents **missing, unknown, or inapplicable data** — **not zero or empty string**.

It’s a **three-valued logic**: `TRUE`, `FALSE`, `UNKNOWN`.

---

### 🔹 25.1 Key Rules

| Expression | Result |
|----------|--------|
| `5 = NULL` | `UNKNOWN` (not `FALSE`) |
| `5 != NULL` | `UNKNOWN` |
| `NULL = NULL` | `UNKNOWN` |
| `WHERE column = NULL` | ❌ Never true — use `IS NULL` |
| `WHERE column != 'value'` | ❌ Excludes `NULL` values |

---

### 🔹 25.2 Correct NULL Checks

```sql
-- ✅ Correct
SELECT * FROM users WHERE email IS NULL;
SELECT * FROM users WHERE email IS NOT NULL;

-- ❌ Wrong
SELECT * FROM users WHERE email = NULL; -- Returns nothing
```

---

### 🔹 25.3 NULL in Aggregates

| Function | Handles NULL? |
|--------|--------------|
| `COUNT(*)` | ✅ Counts all rows |
| `COUNT(column)` | ❌ Ignores NULLs |
| `SUM`, `AVG`, `MIN`, `MAX` | ✅ Ignore NULLs |
| `COALESCE(column, 0)` | ✅ Replace NULL with default |

---

### 🔹 25.4 NULL in Joins

- `LEFT JOIN`: Preserves all rows from left, `NULL` on right if no match
- `INNER JOIN`: Excludes rows where join condition is `NULL`

---

### 🔹 25.5 Best Practices

| Rule | Why |
|------|-----|
| ✅ **Use `IS NULL` / `IS NOT NULL`** | Never `= NULL` |
| ✅ **Use `COALESCE` or `IFNULL`** | Provide defaults |
| ✅ **Avoid NULL in PKs** | Primary keys must be unique and non-null |
| ✅ **Document meaning of NULL** | Is it "unknown" or "not applicable"? |

---

### 📌 Interview Answer

> _"NULL means missing or unknown data — it's not zero or empty. Comparisons with NULL return UNKNOWN, not TRUE/FALSE. I use IS NULL, not = NULL. Aggregates like SUM ignore NULLs. I use COALESCE to provide defaults. NULLs are useful but require care — especially in joins and constraints."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use views for security, stored procedures for complex logic, and handle NULLs with COALESCE to avoid surprises."_  

That shows **deep, practical SQL knowledge**.

