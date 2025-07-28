#  Stored Procedures in Snowflake SQL - JavaScript

---

## Objectives

- Understand what stored procedures are and when to use them
- Learn Snowflake’s JavaScript-based stored procedure syntax
- Create, call, and manage stored procedures in Snowflake
- Use input parameters and return values
- Handle exceptions inside procedures

---

##  Basic Stored Procedure Structure

```sql
CREATE OR REPLACE PROCEDURE sp_greet(name STRING)
RETURNS STRING
LANGUAGE JAVASCRIPT
AS
$$
  return 'Hello, ' + name + '!';
$$;
```

### Creating and Calling a Simple Procedure

```sql
-- Create procedure
CREATE OR REPLACE PROCEDURE sp_add_numbers(a NUMBER, b NUMBER)
RETURNS NUMBER
LANGUAGE JAVASCRIPT
AS
$$
  return a + b;
$$;

-- Call procedure
CALL sp_add_numbers(5, 7);
```

### Using Variables and Control Flow

```sql
CREATE OR REPLACE PROCEDURE sp_factorial(n INT)
RETURNS INT
LANGUAGE JAVASCRIPT
AS
$$
  var result = 1;
  for (var i = 2; i <= n; i++) {
    result *= i;
  }
  return result;
$$;

CALL sp_factorial(5); -- Returns 120
```

### Handling Exceptions


```sql
CREATE OR REPLACE PROCEDURE sp_divide(a NUMBER, b NUMBER)
RETURNS STRING
LANGUAGE JAVASCRIPT
AS
$$
  try {
    if (b === 0) {
      throw "Division by zero error";
    }
    return (a / b).toString();
  } catch(err) {
    return 'Error: ' + err;
  }
$$;

CALL sp_divide(10, 0); -- Returns error message
```

### Executing SQL inside Procedures

```sql
CREATE OR REPLACE PROCEDURE sp_get_customer_count()
RETURNS NUMBER
LANGUAGE JAVASCRIPT
AS
$$
  var sql_command = "SELECT COUNT(*) FROM customers";
  var stmt = snowflake.createStatement({sqlText: sql_command});
  var result = stmt.execute();
  result.next();
  return result.getColumnValue(1);
$$;

CALL sp_get_customer_count();
```

| Feature          | Description                       | Example                      |
| ---------------- | --------------------------------- | ---------------------------- |
| Create Procedure | `CREATE OR REPLACE PROCEDURE ...` | Use `LANGUAGE JAVASCRIPT`    |
| Input Parameters | Pass values into procedures       | `(a NUMBER, b NUMBER)`       |
| Return Value     | Use `RETURNS` keyword             | `RETURNS NUMBER`             |
| Control Flow     | Use JavaScript (loops, if-else)   | `for`, `try-catch`           |
| Execute SQL      | Use `snowflake.createStatement()` | `stmt.execute()`             |
| Call Procedure   | `CALL procedure_name(args)`       | `CALL sp_add_numbers(5, 7);` |
