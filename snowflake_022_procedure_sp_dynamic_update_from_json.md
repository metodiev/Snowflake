Write a procedure that accepts a JSON input string to update multiple columns dynamically in a target table.

## This procedure assumes:

You provide a JSON string with keys = column names and values = new values

You identify the row to update using a primary key value

### Example JSON input:

```json
{
  "customer_name": "John Doe",
  "email": "john@example.com",
  "status": "active"
}
```

```sql
CREATE OR REPLACE PROCEDURE sp_dynamic_update_from_json(
  target_table STRING,
  pk_column STRING,
  pk_value STRING,
  update_data STRING  -- JSON as string
)
RETURNS STRING
LANGUAGE SQL
AS
$$
DECLARE
  json_obj OBJECT;
  col_list STRING;
  set_clauses STRING;
  sql_text STRING;
  key STRING;
BEGIN
  json_obj := PARSE_JSON(update_data);
  set_clauses := '';

  FOR key IN (SELECT KEY FROM TABLE(FLATTEN(input => json_obj))) DO
    set_clauses := set_clauses || key.VALUE || ' = ' || QUOTE_LITERAL(json_obj:key.VALUE) || ', ';
  END FOR;

  -- Remove trailing comma and space
  set_clauses := RTRIM(set_clauses, ', ');

  sql_text := 'UPDATE ' || target_table || 
              ' SET ' || set_clauses || 
              ' WHERE ' || pk_column || ' = ' || QUOTE_LITERAL(pk_value) || ';';

  EXECUTE IMMEDIATE sql_text;

  RETURN 'Update successful';
END;
$$;
```

### Example Call:

```sql
CALL sp_dynamic_update_from_json(
  'customers',
  'customer_id',
  'CUST123',
  '{"customer_name":"Jane Smith","email":"jane@acme.com","status":"active"}'
);
```

- Automatically updates only the columns provided in the JSON

- Uses dynamic SQL to build the SET clause

- Can be extended to validate column names or types before execution