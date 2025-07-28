# Conditional Logic in Snowflake SQL (CASE, IFF, DECODE)

---

## Objectives

- Apply conditional logic in SQL queries using:
  - `CASE`
  - `IFF()`
  - `DECODE()`
- Understand when and why to use each
- Build readable, dynamic SQL using logic branching

---

## Prerequisites

Use the following `employees` table:

```sql
CREATE OR REPLACE TABLE employees (
    emp_id INT,
    name STRING,
    department STRING,
    salary NUMBER(10,2)
);

INSERT INTO employees VALUES
(1, 'Alice', 'Engineering', 120000),
(2, 'Bob', 'Sales', 80000),
(3, 'Carol', 'HR', 60000),
(4, 'Dave', 'Engineering', 95000),
(5, 'Eve', 'Sales', 70000);

```

### CASE Statement
Provides IF-ELSE logic directly in SQL.

```sql
SELECT
  name,
  department,
  salary,
  CASE
    WHEN salary >= 100000 THEN 'High'
    WHEN salary BETWEEN 70000 AND 99999 THEN 'Medium'
    ELSE 'Low'
  END AS salary_band
FROM employees;
```


### IFF() Function
Shorthand for IF(condition, true_value, false_value) in Snowflake.###

```sql
SELECT
  name,
  salary,
  IFF(salary > 90000, 'Above Average', 'Below Average') AS salary_eval
FROM employees;
```

### DECODE() Function
Acts like a simplified SWITCH CASE.

```sql
SELECT
  name,
  department,
  DECODE(department,
         'Engineering', 'ENG',
         'Sales', 'SLS',
         'HR', 'HUM',
         'OTH') AS dept_code
FROM employees;
```

| Function   | Description                        | Use Case Example                    |
| ---------- | ---------------------------------- | ----------------------------------- |
| `CASE`     | Multi-condition logic (if-elseif)  | Complex value branching             |
| `IFF()`    | Single-condition shortcut          | `IFF(salary > 100000, 'Yes', 'No')` |
| `DECODE()` | Like switch/case for value mapping | `DECODE(dept, 'Sales', 'SLS', ...)` |
