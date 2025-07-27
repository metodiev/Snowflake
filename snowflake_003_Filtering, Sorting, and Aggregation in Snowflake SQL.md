# Prompt 3: Filtering, Sorting, and Aggregation in Snowflake SQL

---

##  Objectives

Learn how to:

- Filter rows using `WHERE`
- Sort results using `ORDER BY`
- Aggregate data using functions like `COUNT()`, `SUM()`, `AVG()`
- Use `GROUP BY` and `HAVING` for grouped filtering

---

##  Prerequisites

We continue using the `employees` table:

```sql
CREATE OR REPLACE TABLE employees (
    id INT,
    first_name STRING,
    last_name STRING,
    department STRING,
    salary FLOAT
);
```

### Filtering with WHERE

```sql
-- Employees with salary greater than 90k
SELECT * FROM employees
WHERE salary > 90000;

-- Employees in Engineering or Product departments
SELECT * FROM employees
WHERE department IN ('Engineering', 'Product');

-- Employees whose name starts with 'A'
SELECT * FROM employees
WHERE first_name LIKE 'A%';
```

### Sorting with ORDER BY

```sql
-- Highest-paid employees first
SELECT * FROM employees
ORDER BY salary DESC;

-- Sort by department, then by last name
SELECT * FROM employees
ORDER BY department, last_name;
```


### Aggregation Functions

```sql
-- Total salary cost
SELECT SUM(salary) AS total_salary FROM employees;

-- Average salary
SELECT AVG(salary) AS average_salary FROM employees;

-- Count of employees
SELECT COUNT(*) AS total_employees FROM employees;

-- Min and Max salary
SELECT MIN(salary) AS lowest, MAX(salary) AS highest FROM employees;
```

### Grouping with GROUP BY

```sql
-- Average salary per department
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department;

-- Count of employees in each department
SELECT department, COUNT(*) AS dept_count
FROM employees
GROUP BY department;
```

### Filtering Groups with HAVING

```sql
-- Departments with more than 1 employee
SELECT department, COUNT(*) AS num_employees
FROM employees
GROUP BY department
HAVING COUNT(*) > 1;

-- Departments with average salary above 90k
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 90000;
```