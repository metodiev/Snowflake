# Subqueries and Common Table Expressions (CTEs) in Snowflake SQL

---

##  Objectives

In this prompt, you'll learn how to:

- Use different types of **subqueries**:
  - Inline
  - Scalar
  - Correlated
- Create and apply **CTEs (Common Table Expressions)** using the `WITH` clause
- Understand when to use subqueries vs. CTEs

---

## Prerequisites

Make sure these tables exist in your Snowflake environment:

```sql
CREATE OR REPLACE TABLE employees (
    id INT,
    first_name STRING,
    department_id INT,
    salary FLOAT
);

CREATE OR REPLACE TABLE departments (
    department_id INT,
    department_name STRING
);
```

## Subqueries

Subqueries are queries nested inside another query. There are different types:

### Inline Subquery

Used inside the FROM or SELECT clause.

```sql
-- Top 3 highest earners
SELECT *
FROM (
    SELECT * FROM employees
    ORDER BY salary DESC
    LIMIT 3
) AS top_earners;
```

### Scalar Subquery

Returns a single value, often used inside the SELECT or WHERE clause.

```sql
-- Compare employee salary to overall average
SELECT first_name, salary,
  (SELECT AVG(salary) FROM employees) AS avg_salary
FROM employees;

```

### Correlated Subquery

```sql
-- Employees earning more than the average in their department
SELECT e.first_name, e.salary
FROM employees e
WHERE e.salary > (
    SELECT AVG(salary)
    FROM employees
    WHERE department_id = e.department_id
);
```

## Common Table Expressions (CTEs)

### Basic CTE

```sql
WITH engineering_employees AS (
    SELECT * FROM employees
    WHERE department_id = 1
)
SELECT first_name, salary
FROM engineering_employees
ORDER BY salary DESC;

```

### CTE with Aggregation

```sql
WITH dept_avg AS (
    SELECT department_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
)
SELECT e.first_name, e.salary, d.avg_salary
FROM employees e
JOIN dept_avg d ON e.department_id = d.department_id
WHERE e.salary > d.avg_salary;
```

## When to Use: Subqueries vs. CTEs

| Use Case                          | Prefer   |
| --------------------------------- | -------- |
| Complex multi-step queries        | CTE      |
| One-time filtering logic          | Subquery |
| Better readability and modularity | CTE      |
| Recursive queries (coming soon)   | CTE      |


