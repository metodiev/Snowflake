#  Set Operations in Snowflake SQL — UNION, INTERSECT, MINUS

---

##  Objectives

Learn how to use SQL set operations to combine results from multiple queries:

- `UNION` and `UNION ALL`
- `INTERSECT`
- `MINUS` (also called EXCEPT)

---

## Prerequisites

Create these sample tables for practice:

```sql
CREATE OR REPLACE TABLE table_a (id INT, value STRING);
CREATE OR REPLACE TABLE table_b (id INT, value STRING);

INSERT INTO table_a VALUES
(1, 'Apple'),
(2, 'Banana'),
(3, 'Cherry');

INSERT INTO table_b VALUES
(2, 'Banana'),
(3, 'Cherry'),
(4, 'Date');

```

### UNION and UNION ALL
UNION combines results from two queries and removes duplicates.

UNION ALL combines results and includes duplicates (faster, no duplicate check).

```sql

-- UNION removes duplicates
SELECT * FROM table_a
UNION
SELECT * FROM table_b;


-- UNION ALL keeps duplicates
SELECT * FROM table_a
UNION ALL
SELECT * FROM table_b;

```

### INTERSECT
Returns rows that appear in both queries.

```sql
SELECT * FROM table_a
INTERSECT
SELECT * FROM table_b;
```

### MINUS (EXCEPT)
```sql
SELECT * FROM table_a
MINUS
SELECT * FROM table_b;
```


| Operation   | Description                               | Removes Duplicates? |
| ----------- | ----------------------------------------- | ------------------- |
| `UNION`     | Combines results, removes duplicates      | Yes                 |
| `UNION ALL` | Combines results, keeps duplicates        | No                  |
| `INTERSECT` | Returns common rows                       | N/A                 |
| `MINUS`     | Returns rows in first query not in second | N/A                 |
    
