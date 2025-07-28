Write a procedure to generate dynamic SQL that creates or replaces views for all tables in a given schema.

- Automatically generate read-only views for every table in a specific schema (e.g., for exposing data to reporting or BI tools). You might want to:

- Prefix views with v_

- Place them in a different schema (e.g., reporting)

- Make it dynamic and reusable

```sql
CREATE OR REPLACE PROCEDURE sp_generate_views_for_schema(
    source_db STRING,
    source_schema STRING,
    target_schema STRING
)
RETURNS STRING
LANGUAGE SQL
AS
$$
DECLARE
  tbl_name STRING;
  view_sql STRING;
  full_tbl_name STRING;
  view_name STRING;

  cur_tbls CURSOR FOR
    SELECT table_name 
    FROM information_schema.tables
    WHERE table_schema = source_schema 
      AND table_type = 'BASE TABLE'
      AND table_catalog = source_db;
BEGIN
  FOR tbl_row IN cur_tbls DO
    tbl_name := tbl_row.table_name;
    full_tbl_name := source_db || '.' || source_schema || '.' || tbl_name;
    view_name := target_schema || '.v_' || tbl_name;

    view_sql := 'CREATE OR REPLACE VIEW ' || view_name || ' AS SELECT * FROM ' || full_tbl_name || ';';

    -- Execute the dynamic SQL
    EXECUTE IMMEDIATE view_sql;
  END FOR;

  RETURN 'Views created for schema: ' || source_schema;
END;
$$;
```

### Example Call:
```sql
CALL sp_generate_views_for_schema('MY_DB', 'RAW_DATA', 'REPORTING');
This will:
```

- Loop through all tables in MY_DB.RAW_DATA

- Create or replace views like REPORTING.v_customers, REPORTING.v_orders, etc.

- Each view will do SELECT * FROM RAW_DATA.table_name