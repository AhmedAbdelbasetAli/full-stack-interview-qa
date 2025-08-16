Absolutely! Here's a **comprehensive guide to essential SQL keywords**, what they do, and **when to use them** — tailored for **interview preparation, problem-solving, and real-world usage**.

This list covers the **most important SQL keywords** you’ll encounter on **LeetCode, HackerRank, and in technical interviews**.

---

## 🧩 Core SQL Keywords (with Purpose & Usage)

### 1. `SELECT`
- **What**: Retrieves data from a database.
- **When to use**: Always the starting point to **query data**.
- **Example**:
  ```sql
  SELECT name, salary FROM Employee;
  ```

---

### 2. `FROM`
- **What**: Specifies the table(s) to retrieve data from.
- **When to use**: After `SELECT`, to indicate source table.
- **Example**:
  ```sql
  SELECT * FROM Employee;
  ```

---

### 3. `WHERE`
- **What**: Filters rows **before grouping** based on conditions.
- **When to use**: To filter individual rows (e.g., `salary > 50000`).
- **Can use with**: `=`, `<>`, `<`, `>`, `IN`, `BETWEEN`, `LIKE`, `IS NULL`, etc.
- **Example**:
  ```sql
  SELECT name FROM Employee WHERE salary > 70000;
  ```

> ❌ Cannot use aggregate functions like `COUNT()` in `WHERE`.

---

### 4. `GROUP BY`
- **What**: Groups rows that have the same values into summary rows.
- **When to use**: Before using aggregate functions (`COUNT`, `SUM`, `AVG`, etc.) per group.
- **Example**:
  ```sql
  SELECT departmentId, AVG(salary)
  FROM Employee
  GROUP BY departmentId;
  ```

> ✅ Always use with aggregate functions.

---

### 5. `HAVING`
- **What**: Filters groups **after** `GROUP BY` (like `WHERE` for groups).
- **When to use**: To filter on **aggregated values** (e.g., `HAVING COUNT(*) > 1`).
- **Example**:
  ```sql
  SELECT departmentId, COUNT(*)
  FROM Employee
  GROUP BY departmentId
  HAVING COUNT(*) > 1;
  ```

> ✅ Use `HAVING` instead of `WHERE` when filtering on `COUNT`, `SUM`, etc.

---

### 6. `ORDER BY`
- **What**: Sorts the result set by one or more columns.
- **When to use**: At the end of query to sort output.
- **Options**: `ASC` (default), `DESC`
- **Example**:
  ```sql
  SELECT name FROM Employee ORDER BY salary DESC;
  ```

---

### 7. `LIMIT` and `OFFSET`
- **`LIMIT n`**: Returns only `n` rows.
- **`OFFSET m`**: Skips `m` rows before starting to return.
- **When to use**: Pagination, top-N queries, finding Nth highest.
- **Example**:
  ```sql
  SELECT salary FROM Employee ORDER BY salary DESC LIMIT 1 OFFSET 1;
  -- Gets 2nd highest salary
  ```

---

### 8. `DISTINCT`
- **What**: Removes duplicate values from the result.
- **When to use**: When you want **unique values only**.
- **Example**:
  ```sql
  SELECT DISTINCT departmentId FROM Employee;
  ```

---

### 9. `JOIN` (Types)

| Keyword | What It Does | When to Use |
|--------|---------------|-------------|
| `INNER JOIN` | Returns rows with matches in **both tables** | When you need only matching records |
| `LEFT JOIN` | Returns **all rows from left table**, matched rows from right (or `NULL`) | When you want all records from one table, even if no match |
| `RIGHT JOIN` | Opposite of `LEFT JOIN` | Rarely used; can rewrite as `LEFT JOIN` |
| `FULL OUTER JOIN` | Returns all rows from both tables | Not supported in MySQL; use `UNION` of `LEFT` and `RIGHT` |

- **Example**:
  ```sql
  SELECT e.name, d.name
  FROM Employee e
  LEFT JOIN Department d ON e.departmentId = d.id;
  ```

---

### 10. `ON` vs `USING`
- **`ON`**: Specifies join condition (e.g., `ON e.id = d.employeeId`)
- **`USING`**: Shortcut when column names are identical (e.g., `USING (id)`)

---

### 11. `UNION` and `UNION ALL`
- **`UNION`**: Combines results of two `SELECT` statements, removes duplicates.
- **`UNION ALL`**: Combines results, **keeps duplicates** (faster).
- **When to use**: Combine data from similar tables or queries.
- **Rules**: Both queries must have same number of columns and compatible types.
- **Example**:
  ```sql
  SELECT name FROM Managers
  UNION
  SELECT name FROM Engineers;
  ```

---

### 12. Aggregate Functions
These **work with `GROUP BY` and `HAVING`**:

| Function | Purpose | Example |
|--------|--------|--------|
| `COUNT()` | Count rows | `COUNT(*)`, `COUNT(email)` |
| `SUM()` | Add values | `SUM(salary)` |
| `AVG()` | Average | `AVG(price)` |
| `MAX()` | Highest value | `MAX(salary)` |
| `MIN()` | Lowest value | `MIN(age)` |

> ❗ Cannot use in `WHERE`. Use in `SELECT` or `HAVING`.

---

### 13. Window Functions (Advanced, but Interview Favorite)

| Function | Purpose | Example |
|--------|--------|--------|
| `ROW_NUMBER()` | Unique rank, even ties | `ROW_NUMBER() OVER (PARTITION BY dept ORDER BY sal DESC)` |
| `RANK()` | Rank with gaps on ties | Tied 1st → next is 3rd |
| `DENSE_RANK()` | Rank without gaps | Tied 1st → next is 2nd |
| `LAG()` | Previous row value | `LAG(salary, 1) OVER (ORDER BY id)` |
| `LEAD()` | Next row value | `LEAD(salary, 1) OVER (...)` |
| `FIRST_VALUE()` | First value in window | `FIRST_VALUE(name) OVER (...)` |

- **When to use**: Top-N per group, running totals, comparing with previous row.

---

### 14. `CASE WHEN`
- **What**: Conditional logic in SQL (like `if-else`).
- **When to use**: Categorize data, compute derived columns.
- **Example**:
  ```sql
  SELECT name,
    CASE 
      WHEN salary > 80000 THEN 'High'
      WHEN salary > 50000 THEN 'Medium'
      ELSE 'Low'
    END AS salary_level
  FROM Employee;
  ```

---

### 15. `EXISTS` / `NOT EXISTS`
- **What**: Checks if a subquery returns **any rows**.
- **When to use**: Efficient for checking existence (better than `IN` sometimes).
- **Example**:
  ```sql
  SELECT name FROM Employee e
  WHERE EXISTS (
    SELECT 1 FROM Department d
    WHERE d.id = e.departmentId AND d.name = 'IT'
  );
  ```

---

### 16. `IN` / `NOT IN`
- **`IN`**: Checks if value is in a list or subquery.
- **Use with caution**: `NOT IN` fails if subquery returns `NULL`.
- **Example**:
  ```sql
  SELECT name FROM Employee WHERE departmentId IN (1, 2);
  ```

---

### 17. `BETWEEN`, `LIKE`, `IS NULL`
- **`BETWEEN a AND b`**: Inclusive range (works with numbers, dates)
  ```sql
  WHERE salary BETWEEN 50000 AND 100000
  ```
- **`LIKE`**: Pattern matching
  ```sql
  WHERE name LIKE 'J%'  -- Starts with J
  WHERE name LIKE '%oh%' -- Contains "oh"
  ```
- **`IS NULL` / `IS NOT NULL`**: Check for nulls
  ```sql
  WHERE email IS NULL
  ```

---

### 18. `AS`
- **What**: Alias for columns or tables.
- **When to use**: Improve readability or rename output columns.
- **Example**:
  ```sql
  SELECT salary AS pay FROM Employee;
  SELECT e.name, d.name
  FROM Employee AS e
  JOIN Department AS d ON e.deptId = d.id;
  ```

---

### 19. `CREATE`, `INSERT`, `UPDATE`, `DELETE`

| Keyword | Purpose | Example |
|--------|--------|--------|
| `CREATE TABLE` | Define new table | `CREATE TABLE Employee (...);` |
| `INSERT INTO` | Add new rows | `INSERT INTO Employee VALUES (1, 'Alice', 70000);` |
| `UPDATE` | Modify existing rows | `UPDATE Employee SET salary = 80000 WHERE id = 1;` |
| `DELETE FROM` | Remove rows | `DELETE FROM Employee WHERE id = 1;` |

> 🔒 Use `WHERE` with `UPDATE`/`DELETE` — or you’ll affect all rows!

---

### 20. `PRIMARY KEY`, `FOREIGN KEY`
- **`PRIMARY KEY`**: Unique identifier for a row (e.g., `id`)
- **`FOREIGN KEY`**: References primary key in another table (enforces referential integrity)

---

### 21. `COALESCE`
- **What**: Returns first non-NULL value in a list.
- **When to use**: Handle `NULL`s, provide defaults.
- **Example**:
  ```sql
  SELECT COALESCE(phone, 'Not Provided') FROM Employee;
  ```

---

### 22. `NULL` Handling
- `NULL` means "missing or unknown"
- Never use `= NULL`, use `IS NULL`
- Any operation with `NULL` → `NULL` (e.g., `5 + NULL = NULL`)

---

## 🧠 When to Use Which? Quick Decision Guide

| Goal | Keywords to Use |
|------|-----------------|
| Get data | `SELECT`, `FROM`, `WHERE` |
| Remove duplicates | `DISTINCT` |
| Group data | `GROUP BY`, `HAVING` |
| Sort results | `ORDER BY` |
| Top-N or Nth highest | `ORDER BY`, `LIMIT`, `OFFSET`, `DENSE_RANK()` |
| Combine tables | `JOIN` (`INNER`, `LEFT`) |
| Compare with previous row | `LAG()`, `LEAD()` |
| Find max per group | `MAX() OVER (PARTITION BY ...)` |
| Handle conditions | `CASE WHEN` |
| Safe existence check | `EXISTS` |
| Pagination | `LIMIT`, `OFFSET` |

---

## 📚 Summary: Must-Know for Interviews

| Category | Keywords |
|--------|---------|
| **Basics** | `SELECT`, `FROM`, `WHERE`, `ORDER BY`, `DISTINCT` |
| **Aggregation** | `GROUP BY`, `HAVING`, `COUNT`, `SUM`, `AVG`, `MAX`, `MIN` |
| **Joins** | `INNER JOIN`, `LEFT JOIN`, `ON` |
| **Advanced** | `OVER`, `PARTITION BY`, `RANK`, `DENSE_RANK`, `LAG`, `LEAD` |
| **Logic** | `CASE WHEN`, `COALESCE`, `IS NULL`, `IN`, `EXISTS` |
| **Control** | `LIMIT`, `OFFSET`, `UNION` |

---

## ✅ Pro Tips

1. **`WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`**  
   This is the **logical order of execution** (not the writing order!).

2. **Always alias tables** in joins (`e`, `d`) — makes code cleaner.

3. **Use `LEFT JOIN` + `WHERE ... IS NULL`** to find missing records (e.g., "employees without address").

4. **Use `DENSE_RANK()`** for "top N per group" or "Nth highest distinct".

5. **Test edge cases**: empty tables, `NULL`s, duplicates, ties.

---
