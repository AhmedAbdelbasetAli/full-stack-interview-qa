Absolutely! Here are the **10 most famous and frequently asked SQL interview questions** across top tech companies like **Amazon, Google, Meta (Facebook), Netflix, Apple, Uber, and LinkedIn**.

These problems test **core SQL concepts** like joins, aggregations, window functions, ranking, and edge-case handling — and appear **over and over** in real interviews.

---

## 🏆 The 10 Most Famous SQL Interview Questions

---

### 1. **Second Highest Salary**  
**LeetCode**: [176. Second Highest Salary](https://leetcode.com/problems/second-highest-salary/)  
**Problem**: Find the second highest salary. Return `NULL` if it doesn’t exist.  
**Why Famous**: Tests knowledge of `DISTINCT`, `ORDER BY`, `LIMIT/OFFSET`, and **handling edge cases**.  
**Key Concepts**:  
- `LIMIT 1 OFFSET 1`  
- Scalar subquery returns `NULL` naturally  
- Avoid using `WHERE` with aggregates  

✅ **Sample Answer**:
```sql
SELECT (
    SELECT DISTINCT salary 
    FROM Employee 
    ORDER BY salary DESC 
    LIMIT 1 OFFSET 1
) AS SecondHighestSalary;
```

---

### 2. **Employees Earning More Than Their Managers**  
**LeetCode**: [181. Employees Earning More Than Their Managers](https://leetcode.com/problems/employees-earning-more-than-their-managers/)  
**Problem**: Find employees whose salary is higher than their manager’s.  
**Why Famous**: Classic **self-join** problem.  
**Key Concepts**:  
- Self-join on `id` and `managerId`  
- Compare values across rows  

✅ **Sample Answer**:
```sql
SELECT e.name AS Employee
FROM Employee e
JOIN Employee m ON e.managerId = m.id
WHERE e.salary > m.salary;
```

---

### 3. **Duplicate Emails**  
**LeetCode**: [182. Duplicate Emails](https://leetcode.com/problems/duplicate-emails/)  
**Problem**: Report all duplicate emails.  
**Why Famous**: Simple but tests `GROUP BY` and `HAVING`.  
**Key Concepts**:  
- `GROUP BY email`  
- `HAVING COUNT(*) > 1`  

✅ **Sample Answer**:
```sql
SELECT email AS Email
FROM Person
GROUP BY email
HAVING COUNT(*) > 1;
```

---

### 4. **Customers Who Never Order**  
**LeetCode**: [183. Customers Who Never Order](https://leetcode.com/problems/customers-who-never-order/)  
**Problem**: Find customers who never placed an order.  
**Why Famous**: Tests understanding of **`LEFT JOIN` vs `NOT IN` vs `NOT EXISTS`**.  
**Key Concepts**:  
- `LEFT JOIN` + `WHERE right.id IS NULL`  
- Danger of `NOT IN` with `NULL`  

✅ **Best Answer**:
```sql
SELECT c.name AS Customers
FROM Customers c
LEFT JOIN Orders o ON c.id = o.customerId
WHERE o.customerId IS NULL;
```

---

### 5. **Department Highest Salary**  
**LeetCode**: [184. Department Highest Salary](https://leetcode.com/problems/department-highest-salary/)  
**Problem**: Find employees with the highest salary in each department.  
**Why Famous**: One of the **most asked** — tests **window functions** and joins.  
**Key Concepts**:  
- `MAX() OVER (PARTITION BY departmentId)`  
- Or `RANK()` / `DENSE_RANK()`  
- Join with `Department` table  

✅ **Sample Answer**:
```sql
SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary
FROM (
    SELECT name, salary, departmentId,
           MAX(salary) OVER (PARTITION BY departmentId) AS max_sal
    FROM Employee
) e
JOIN Department d ON e.departmentId = d.id
WHERE e.salary = e.max_sal;
```

---

### 6. **Consecutive Numbers**  
**LeetCode**: [180. Consecutive Numbers](https://leetcode.com/problems/consecutive-numbers/)  
**Problem**: Find numbers that appear at least 3 times consecutively.  
**Why Famous**: Tests **pattern matching across rows**.  
**Key Concepts**:  
- Self-join: `id`, `id+1`, `id+2`  
- Or `LAG()` / `LEAD()`  

✅ **Sample Answer**:
```sql
SELECT DISTINCT l1.num AS ConsecutiveNums
FROM Logs l1
JOIN Logs l2 ON l1.id + 1 = l2.id
JOIN Logs l3 ON l1.id + 2 = l3.id
WHERE l1.num = l2.num AND l2.num = l3.num;
```

---

### 7. **Nth Highest Salary**  
**LeetCode**: [177. Nth Highest Salary](https://leetcode.com/problems/nth-highest-salary/)  
**Problem**: Write a function to find the Nth highest distinct salary.  
**Why Famous**: Generalization of "Second Highest" — tests **functions, variables, OFFSET**.  
**Key Concepts**:  
- `LIMIT 1 OFFSET N-1`  
- Variable handling in MySQL  
- Use of `RETURN (SELECT ...)` to auto-return `NULL`  

✅ **Sample Answer**:
```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
    SELECT DISTINCT salary
    FROM Employee
    ORDER BY salary DESC
    LIMIT 1 OFFSET N - 1
  );
END;
```

---

### 8. **Rising Temperature**  
**LeetCode**: [197. Rising Temperature](https://leetcode.com/problems/rising-temperature/)  
**Problem**: Find days where temperature was higher than the previous day.  
**Why Famous**: Classic **row-to-row comparison**.  
**Key Concepts**:  
- Self-join with `DATEDIFF(t1.date, t2.date) = 1`  
- Or `LAG()`  

✅ **Sample Answer**:
```sql
SELECT t1.id
FROM Weather t1
JOIN Weather t2 ON DATEDIFF(t1.recordDate, t2.recordDate) = 1
WHERE t1.temperature > t2.temperature;
```

---

### 9. **Trips and Users (Hard but Famous)**  
**LeetCode**: [262. Trips and Users](https://leetcode.com/problems/trips-and-users/)  
**Problem**: Report cancellation rate per day, excluding banned users.  
**Why Famous**: Asked at **Uber, Lyft, DoorDash** — tests **joins, filtering, ROUND(), conditional aggregation**.  
**Key Concepts**:  
- Multiple `JOIN`s  
- `BETWEEN` for date range  
- `ROUND(AVG(condition), 2)`  

✅ **Pro Tip**: Use `CASE WHEN status LIKE 'cancelled%' THEN 1 ELSE 0 END`

---

### 10. **Sales Analysis or Product Revenue Reports**  
**LeetCode**: [1069. Product Sales Analysis II](https://leetcode.com/problems/product-sales-analysis-ii/)  
**Problem**: Sum quantity per product, or average price, etc.  
**Why Famous**: Real-world **business analytics** question.  
**Key Concepts**:  
- `GROUP BY product_id`  
- `SUM(quantity)`, `SUM(price * quantity)`  
- Often combined with date filtering  

✅ **Sample Pattern**:
```sql
SELECT product_id, SUM(quantity) AS total_qty
FROM Sales
GROUP BY product_id;
```

---

## 📊 Summary Table

| # | Problem | Key SQL Skill | Frequency |
|----|--------|---------------|-----------|
| 1 | Second Highest Salary | `LIMIT`, `OFFSET`, edge cases | ⭐⭐⭐⭐⭐ |
| 2 | Employees > Managers | Self-join | ⭐⭐⭐⭐⭐ |
| 3 | Duplicate Emails | `GROUP BY`, `HAVING` | ⭐⭐⭐⭐☆ |
| 4 | Customers No Orders | `LEFT JOIN`, `NOT EXISTS` | ⭐⭐⭐⭐⭐ |
| 5 | Dept Highest Salary | Window functions | ⭐⭐⭐⭐⭐ |
| 6 | Consecutive Numbers | Self-join or `LAG()` | ⭐⭐⭐⭐☆ |
| 7 | Nth Highest Salary | Functions, `OFFSET` | ⭐⭐⭐⭐☆ |
| 8 | Rising Temperature | Date comparison | ⭐⭐⭐⭐☆ |
| 9 | Trips and Users | Complex filtering, `AVG()` | ⭐⭐⭐⭐☆ (Uber favorite) |
| 10 | Sales Analysis | Aggregation, `GROUP BY` | ⭐⭐⭐⭐☆ |

---

## 💡 Pro Tips for Acing SQL Interviews

1. **Explain your approach first** — interviewers care about logic, not just code.
2. **Handle edge cases**: `NULL`s, duplicates, empty tables.
3. **Use aliases** and format code cleanly.
4. **Know the difference**:
   - `WHERE` vs `HAVING`
   - `INNER JOIN` vs `LEFT JOIN`
   - `RANK()` vs `DENSE_RANK()`
5. **Practice window functions** — they’re the #1 differentiator.

---

## 🔗 Where to Practice

- **LeetCode Database Tag**: [https://leetcode.com/problemset/database/](https://leetcode.com/problemset/database/)
- Filter by: **Top Interview Questions**, **Easy/Medium**, **High Acceptance**

---
