# 🗄️ SQL Deep Dive (Part 4)  
**WHERE vs HAVING, GROUP BY, ORDER BY, Subqueries & Correlated Queries**

This document covers **essential SQL concepts** — critical for **writing efficient queries**, **data analysis**, and **system design interviews**.

Each section includes:
- ✅ Clear definition
- ✅ In-depth explanation
- ✅ Real-world examples
- ✅ Common pitfalls
- ✅ Interview-ready answers

---

## 16. What is the difference between WHERE and HAVING?

| Feature | `WHERE` | `HAVING` |
|--------|--------|---------|
| **Purpose** | Filters **individual rows** | Filters **groups of rows** |
| **Used With** | All queries | Only with `GROUP BY` |
| **Aggregates** | ❌ Cannot use `COUNT`, `SUM`, etc. | ✅ Can use aggregates |
| **Execution Order** | Before grouping | After grouping |
| **Performance** | Filters early → faster | Filters after aggregation |

---

### 🔹 16.1 Execution Order

```sql
SELECT ... 
FROM ... 
WHERE ...          -- 1. Filter rows
GROUP BY ...       -- 2. Group rows
HAVING ...         -- 3. Filter groups
ORDER BY ...       -- 4. Sort results
```

---

### 🔹 16.2 Example: Orders Table

```sql
-- Table: orders
| id | customer_id | amount | status     |
|----|-------------|--------|------------|
| 1  | 101         | 200    | COMPLETED  |
| 2  | 101         | 150    | COMPLETED  |
| 3  | 102         | 300    | PENDING    |
| 4  | 101         | 100    | CANCELLED  |
```

#### ✅ Use `WHERE`: Filter rows before grouping
```sql
-- Find total spent by customers on COMPLETED orders
SELECT customer_id, SUM(amount) as total
FROM orders
WHERE status = 'COMPLETED'  -- Filters individual rows
GROUP BY customer_id;
```

✅ Result:
```
customer_id | total
101         | 350
```

#### ✅ Use `HAVING`: Filter groups after aggregation
```sql
-- Find customers who spent more than $300 on COMPLETED orders
SELECT customer_id, SUM(amount) as total
FROM orders
WHERE status = 'COMPLETED'
GROUP BY customer_id
HAVING SUM(amount) > 300;  -- Filters groups
```

✅ Result:
```
customer_id | total
101         | 350
```

---

### 📌 Interview Answer

> _"WHERE filters individual rows before grouping, and cannot use aggregate functions. HAVING filters groups after GROUP BY, and can use aggregates like SUM or COUNT. I use WHERE to reduce data early, and HAVING to filter based on group results — like 'customers who spent more than $500'."_  

---

## 17. What is the GROUP BY clause used for?

> `GROUP BY` **groups rows with the same values** into **summary rows**, usually to perform **aggregate calculations**.

It’s used with **aggregate functions** like `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`.

---

### 🔹 17.1 Syntax
```sql
SELECT column, AGGREGATE(column)
FROM table
WHERE condition
GROUP BY column;
```

---

### 🔹 17.2 Example: Sales Data

```sql
-- Table: sales
| product  | region  | amount |
|----------|---------|--------|
| Laptop   | North   | 1000   |
| Mouse    | North   | 50     |
| Laptop   | South   | 1200   |
| Keyboard | North   | 80     |
```

#### ✅ Group by region
```sql
SELECT region, SUM(amount) as total_sales
FROM sales
GROUP BY region;
```

✅ Result:
```
region | total_sales
North  | 1130
South  | 1200
```

#### ✅ Group by multiple columns
```sql
SELECT region, product, COUNT(*) as order_count
FROM sales
GROUP BY region, product;
```

✅ Result:
```
region | product  | order_count
North  | Laptop   | 1
North  | Mouse    | 1
North  | Keyboard | 1
South  | Laptop   | 1
```

---

### 🔹 17.3 Rules

| Rule | Why |
|------|-----|
| ✅ **All non-aggregate columns must be in GROUP BY** | Otherwise, which row's value to show? |
| ✅ **Can group by expressions** | `GROUP BY YEAR(order_date)` |
| ✅ **Use HAVING to filter groups** | Not WHERE for aggregate conditions |

---

### 📌 Interview Answer

> _"GROUP BY groups rows with the same values so I can apply aggregate functions like SUM or COUNT. I use it to get totals by category — like sales by region. All non-aggregate columns in SELECT must be in GROUP BY. I use HAVING to filter groups, like 'regions with sales > $1000'."_  

---

## 18. What is the ORDER BY clause? Can you sort by multiple columns?

> `ORDER BY` **sorts the result set** by one or more columns.

Yes, you can **sort by multiple columns** — the database applies them **left to right**.

---

### 🔹 18.1 Syntax
```sql
SELECT column1, column2
FROM table
ORDER BY column1 [ASC|DESC], column2 [ASC|DESC];
```

- `ASC` = Ascending (default)
- `DESC` = Descending

---

### 🔹 18.2 Example: Employees Table

```sql
-- Table: employees
| name     | department | salary | hire_date  |
|----------|------------|--------|------------|
| Alice    | Engineering| 90000  | 2022-01-15 |
| Bob      | HR         | 70000  | 2021-03-10 |
| Charlie  | Engineering| 95000  | 2020-06-01 |
| Diana    | HR         | 70000  | 2023-02-20 |
```

#### ✅ Sort by salary (descending), then name (ascending)
```sql
SELECT name, department, salary
FROM employees
ORDER BY salary DESC, name ASC;
```

✅ Result:
```
name     | department   | salary
Charlie  | Engineering  | 95000
Alice    | Engineering  | 90000
Bob      | HR           | 70000
Diana    | HR           | 70000
```

> Note: Bob comes before Diana because `name ASC` breaks the tie.

---

### 🔹 18.3 Advanced: Sort by Expression

```sql
-- Sort by length of name
ORDER BY LENGTH(name) DESC

-- Sort by custom order
ORDER BY 
  CASE department 
    WHEN 'Engineering' THEN 1
    WHEN 'HR' THEN 2
    ELSE 3 
  END
```

---

### 📌 Interview Answer

> _"ORDER BY sorts the result set. I can sort by multiple columns — like ORDER BY salary DESC, name ASC. The first column is primary, second breaks ties. I can also sort by expressions, like string length or custom logic with CASE. It’s essential for presenting data in a meaningful order."_  

---

## 19. What is a subquery? Give an example.

> A **subquery** (or **nested query**) is a **query inside another query**.

It can appear in:
- `SELECT`
- `FROM`
- `WHERE`
- `HAVING`

---

### 🔹 19.1 Example: Find employees earning more than average

```sql
SELECT name, salary
FROM employees
WHERE salary > (
    SELECT AVG(salary) 
    FROM employees
);
```

✅ The inner query calculates average salary → outer query compares each employee.

---

### 🔹 19.2 Types of Subqueries

| Type | Example |
|------|--------|
| **Scalar Subquery** | Returns one value (used in `WHERE`, `SELECT`) |
| **Row Subquery** | Returns one row, multiple columns |
| **Table Subquery** | Returns multiple rows (used in `FROM`) |
| **Correlated Subquery** | Depends on outer query (see next section) |

---

### 🔹 19.3 Subquery in FROM (Derived Table)

```sql
SELECT department, avg_salary
FROM (
    SELECT department, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department
) AS dept_avg
WHERE avg_salary > 80000;
```

✅ Subquery creates a temporary table `dept_avg`.

---

### 📌 Interview Answer

> _"A subquery is a query inside another query. I use it to break complex logic into steps — like finding employees above average salary. Subqueries can be in WHERE (scalar), FROM (derived table), or SELECT. They make queries modular, but can be slower than JOINs if not optimized."_  

---

## 20. What is a correlated subquery?

> A **correlated subquery** is a subquery that **references a column from the outer query**.

It’s **executed once for each row** of the outer query — often **slower** than joins.

---

### 🔹 20.1 Example: Find employees earning more than department average

```sql
SELECT e1.name, e1.department, e1.salary
FROM employees e1
WHERE e1.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department = e1.department  -- Correlation: uses outer e1.department
);
```

✅ For each employee, the subquery calculates the average salary **in their department**.

---

### 🔹 20.2 How It Works

1. Database picks a row from outer query (`e1`)
2. Runs subquery using `e1.department`
3. Compares `e1.salary > avg`
4. Repeats for every row

❌ **Performance**: O(n²) in worst case

---

### 🔹 20.3 Rewrite with JOIN (Better Performance)

```sql
SELECT e.name, e.department, e.salary
FROM employees e
JOIN (
    SELECT department, AVG(salary) as avg_sal
    FROM employees
    GROUP BY department
) dept_avg ON e.department = dept_avg.department
WHERE e.salary > dept_avg.avg_sal;
```

✅ This runs in O(n) — much faster.

---

### 🔹 20.4 When to Use Correlated Subqueries

| Use Case | Example |
|--------|--------|
| ✅ **Top-N per group** | "Top 2 earners in each department" |
| ✅ **Existence checks** | `WHERE NOT EXISTS (...)` |
| ✅ **Complex row-by-row logic** | When JOINs are hard to write |

---

### 📌 Interview Answer

> _"A correlated subquery references the outer query and runs once per row — like finding employees above their department's average. It’s flexible but slow. I prefer rewriting with JOINs for performance. I use it only when logic is complex, like 'top N per group' or with EXISTS."_  

---

## ✅ Final Tip

> 🎯 In interviews, **combine concepts**:
> _"I use GROUP BY with HAVING to filter groups, ORDER BY for sorting, and avoid correlated subqueries unless necessary."_  

That shows **deep SQL mastery**.

---
