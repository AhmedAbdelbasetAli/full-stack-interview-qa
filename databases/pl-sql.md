# 🧠 PL/SQL Best Practices  
**Write Clean, Efficient, and Maintainable Oracle Code**

This document covers **PL/SQL best practices** — essential for writing **robust**, **performant**, and **maintainable** stored procedures, functions, and packages in **Oracle Database**.

You’ll learn:
- ✅ Coding standards
- ✅ Performance tips
- ✅ Error handling
- ✅ Security
- ✅ Real-world examples
- ✅ Interview-ready answers

---

## 🔹 What is PL/SQL?

> **PL/SQL (Procedural Language/SQL)** is Oracle’s **procedural extension** to SQL.

It allows you to write:
- ✅ **Stored Procedures**
- ✅ **Functions**
- ✅ **Triggers**
- ✅ **Packages**
- ✅ **Anonymous Blocks**

Used for **business logic**, **batch processing**, and **data integrity**.

---

## ✅ 1. Use Meaningful Names

| Do This | ❌ Avoid |
|--------|--------|
| `calculate_annual_bonus` | `proc1` |
| `validate_employee_data` | `chk` |
| `v_employee_id` | `v1` |

✅ Prefix variables:
- `v_` → local variable
- `p_` → parameter
- `g_` → global (package variable)

---

## ✅ 2. Declare Variables Explicitly

```sql
-- ✅ Good
DECLARE
    v_employee_id   employees.employee_id%TYPE;
    v_salary        employees.salary%TYPE;
    v_dept_name     departments.department_name%TYPE;
BEGIN
    -- Safe, schema changes won't break code
END;
```

✅ Use `%TYPE` to **match column types**  
✅ No hardcoding like `VARCHAR2(100)`

---

## ✅ 3. Always Handle Exceptions

Never let exceptions go unhandled.

```sql
BEGIN
    SELECT salary INTO v_salary FROM employees WHERE employee_id = p_emp_id;
    
    IF v_salary < 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 'Invalid salary');
    END IF;
    
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RAISE_APPLICATION_ERROR(-20002, 'Employee not found');
    WHEN TOO_MANY_ROWS THEN
        RAISE_APPLICATION_ERROR(-20003, 'Multiple employees found');
    WHEN OTHERS THEN
        -- Log error
        DBMS_OUTPUT.PUT_LINE('Error: ' || SQLERRM);
        RAISE; -- Re-raise
END;
```

✅ Use `RAISE_APPLICATION_ERROR` for custom messages  
✅ Log errors before re-raising

---

## ✅ 4. Use Bulk Operations for Performance

Avoid row-by-row processing.

### ❌ Bad: Row-by-Row (Slow)
```sql
FOR emp IN (SELECT * FROM employees) LOOP
    UPDATE payroll SET bonus = emp.salary * 0.1 WHERE emp_id = emp.employee_id;
END LOOP;
```

### ✅ Good: `FORALL` (Fast)
```sql
FORALL i IN 1..emp_ids.COUNT
    UPDATE payroll 
    SET bonus = salaries(i) * 0.1 
    WHERE emp_id = emp_ids(i);
```

✅ Use `BULK COLLECT` + `FORALL` for large datasets

---

## ✅ 5. Minimize Context Switching

Avoid switching between SQL and PL/SQL engine unnecessarily.

### ❌ Bad: Too many SQL calls
```sql
FOR i IN 1..1000 LOOP
    INSERT INTO audit_log VALUES (i, SYSDATE, 'Processed');
END LOOP;
```

### ✅ Good: Single SQL with `FORALL`
```sql
FORALL i IN 1..1000
    INSERT INTO audit_log VALUES (i, SYSDATE, 'Processed');
```

✅ Or better: pure SQL
```sql
INSERT INTO audit_log
SELECT level, SYSDATE, 'Processed'
FROM dual CONNECT BY level <= 1000;
```

---

## ✅ 6. Use Packages for Modularity

Group related logic into **packages**.

```sql
CREATE OR REPLACE PACKAGE emp_mgmt AS
    -- Public declarations
    FUNCTION get_annual_salary(p_emp_id IN NUMBER) RETURN NUMBER;
    PROCEDURE hire_employee(
        p_first_name IN VARCHAR2,
        p_last_name  IN VARCHAR2,
        p_email      IN VARCHAR2,
        p_salary     IN NUMBER,
        p_dept_id    IN NUMBER
    );
    PROCEDURE fire_employee(p_emp_id IN NUMBER);
END emp_mgmt;
/

CREATE OR REPLACE PACKAGE BODY emp_mgmt AS
    -- Private helper
    FUNCTION validate_email(p_email IN VARCHAR2) RETURN BOOLEAN IS
    BEGIN
        RETURN p_email LIKE '%@%';
    END;

    FUNCTION get_annual_salary(p_emp_id IN NUMBER) RETURN NUMBER IS
        v_salary NUMBER;
    BEGIN
        SELECT salary INTO v_salary FROM employees WHERE employee_id = p_emp_id;
        RETURN v_salary * 12;
    END;

    PROCEDURE hire_employee(...) IS
    BEGIN
        IF NOT validate_email(p_email) THEN
            RAISE_APPLICATION_ERROR(-20001, 'Invalid email');
        END IF;
        -- Insert logic
    END;
END emp_mgmt;
/
```

✅ **Advantages**:
- Modularity
- Encapsulation
- Reusability
- Performance (loaded into SGA once)

---

## ✅ 7. Use Bind Variables (Prevent SQL Injection)

```sql
-- ✅ Good
EXECUTE IMMEDIATE 'SELECT salary FROM employees WHERE employee_id = :id' 
    INTO v_salary 
    USING p_emp_id;

-- ❌ Bad (SQL injection risk)
EXECUTE IMMEDIATE 'SELECT salary FROM employees WHERE employee_id = ' || p_emp_id;
```

✅ Always use `USING` with dynamic SQL

---

## ✅ 8. Avoid `SELECT *`

```sql
-- ✅ Good
SELECT employee_id, first_name, last_name 
INTO v_id, v_fname, v_lname 
FROM employees 
WHERE employee_id = p_emp_id;

-- ❌ Bad
SELECT * INTO rec FROM employees WHERE employee_id = p_emp_id;
```

✅ Explicit columns prevent issues if table changes  
✅ Better performance (less data)

---

## ✅ 9. Use `%ROWTYPE` Wisely

```sql
-- ✅ Good for single table
DECLARE
    v_emp employees%ROWTYPE;
BEGIN
    SELECT * INTO v_emp FROM employees WHERE employee_id = 100;
END;

-- ❌ Avoid in joins
-- Use explicit records instead
```

---

## ✅ 10. Optimize Loops

### ✅ Use `BULK COLLECT` with Limit
```sql
DECLARE
    TYPE t_emp IS TABLE OF employees%ROWTYPE;
    v_emps t_emp;
    v_offset NUMBER := 0;
BEGIN
    LOOP
        SELECT * BULK COLLECT INTO v_emps
        FROM employees
        WHERE rownum <= 10000
        OFFSET v_offset ROWS FETCH NEXT 10000 ROWS ONLY;

        EXIT WHEN v_emps.COUNT = 0;

        -- Process batch
        FOR i IN 1..v_emps.COUNT LOOP
            -- Process v_emps(i)
        END LOOP;

        v_offset := v_offset + 10000;
    END LOOP;
END;
```

✅ Prevents memory overflow on large datasets

---

## ✅ 11. Use `PRAGMA AUTONOMOUS_TRANSACTION` Carefully

```sql
CREATE OR REPLACE PROCEDURE log_error(p_msg IN VARCHAR2) IS
    PRAGMA AUTONOMOUS_TRANSACTION;
BEGIN
    INSERT INTO error_log(msg, log_time) VALUES (p_msg, SYSDATE);
    COMMIT; -- Required in autonomous transaction
END;
```

✅ Use for logging, auditing  
❌ Don’t overuse — breaks transaction integrity

---

## ✅ 12. Avoid Hardcoding

```sql
-- ✅ Use constants
CREATE OR REPLACE PACKAGE app_constants AS
    c_tax_rate   CONSTANT NUMBER := 0.08;
    c_max_bonus  CONSTANT NUMBER := 50000;
END;
```

---

## ✅ 13. Document Your Code

```sql
/**
 * Procedure: hire_employee
 * Purpose:  Hire a new employee
 * Author:   John Doe
 * Date:     2025-04-05
 * Params:   p_first_name - First name
 *           p_last_name  - Last name
 *           p_email      - Email (must be unique)
 *           p_salary     - Salary (must be > 0)
 *           p_dept_id    - Department ID
 * Throws:   -20001: Invalid email
 *           -20002: Duplicate email
 */
PROCEDURE hire_employee(...) IS
BEGIN
    ...
END;
```

✅ Use `/** */` for package specs

---

## ✅ 14. Performance Tips

| Rule | Why |
|------|-----|
| ✅ **Use indexes on WHERE, JOIN, ORDER BY** | Faster queries |
| ✅ **Avoid functions in WHERE clause** | `UPPER(name) = 'ALICE'` → can’t use index |
| ✅ **Use `EXISTS` instead of `IN` for large sets** | Often faster |
| ✅ **Gather statistics regularly** | Optimizer needs fresh stats |

---

## 📌 Interview Answer

> _"I follow PL/SQL best practices: meaningful names, %TYPE for safety, bulk operations with FORALL and BULK COLLECT, proper exception handling with RAISE_APPLICATION_ERROR, and modular code using packages. I avoid SQL injection with bind variables, minimize context switching, and document everything. For large datasets, I process in batches to avoid memory issues."_  

---

## ✅ Final Tip

> 🎯 In interviews, **say this**:
> _"I don’t write row-by-row loops — I use BULK COLLECT and FORALL for performance. And I always handle exceptions, never let them go uncaught."_  

That shows **deep, production-ready PL/SQL knowledge**.

---
