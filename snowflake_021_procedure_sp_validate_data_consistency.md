Implement a procedure to validate data consistency between two tables and return discrepancies.

```sql
CREATE OR REPLACE PROCEDURE sp_validate_data_consistency(
  table1 STRING,
  table2 STRING,
  key_column STRING,
  columns_to_compare ARRAY
)
RETURNS TABLE (key_val STRING, column_name STRING, table1_value STRING, table2_value STRING)
LANGUAGE SQL
AS
$$
DECLARE
  col STRING;
  sql_text STRING;
BEGIN
  -- Build UNION ALL of all columns differences dynamically
  sql_text := '';
  FOR i IN 0 .. ARRAY_SIZE(columns_to_compare)-1 DO
    LET col := columns_to_compare[i];
    sql_text := sql_text || 
    'SELECT ' || key_column || ' AS key_val, ''' || col || ''' AS column_name, ' ||
    'CAST(t1.' || col || ' AS STRING) AS table1_value, ' ||
    'CAST(t2.' || col || ' AS STRING) AS table2_value ' ||
    'FROM ' || table1 || ' t1 FULL OUTER JOIN ' || table2 || ' t2 USING (' || key_column || ') ' ||
    'WHERE NVL(t1.' || col || ', ''NULL'') <> NVL(t2.' || col || ', ''NULL'') ';

    IF i < ARRAY_SIZE(columns_to_compare)-1 THEN
      sql_text := sql_text || ' UNION ALL ';
    END IF;
  END FOR;

  RETURN TABLE(EXECUTE IMMEDIATE :sql_text);
END;
$$;
```

- Accepts two table names, a key column, and columns to compare

- Dynamically builds a query to find mismatches in specified columns

- Uses FULL OUTER JOIN and compares values, treating NULLs properly

- Returns discrepancies with key, column, and differing values