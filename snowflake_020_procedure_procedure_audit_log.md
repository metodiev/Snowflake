Develop a procedure that logs procedure execution metadata (start time, end time, status) into an audit table.

First, create an audit table if you don’t have one:
```sql
CREATE OR REPLACE TABLE procedure_audit_log (
    procedure_name STRING,
    execution_start TIMESTAMP,
    execution_end TIMESTAMP,
    status STRING,
    error_message STRING
);
```

Now, the procedure template to log execution:

```sql
CREATE OR REPLACE PROCEDURE sp_sample_procedure_with_audit()
RETURNS STRING
LANGUAGE SQL
AS
$$
DECLARE
    start_time TIMESTAMP;
    end_time TIMESTAMP;
    exec_status STRING;
    err_msg STRING;
BEGIN
    start_time := CURRENT_TIMESTAMP;
    exec_status := 'SUCCESS';
    err_msg := NULL;

    BEGIN
        -- Your procedure logic here
        -- Example: simple SELECT or UPDATE statement
        -- SELECT COUNT(*) FROM sales;
    EXCEPTION
        WHEN ERROR THEN
            exec_status := 'FAILED';
            err_msg := SQLERRM;
    END;

    end_time := CURRENT_TIMESTAMP;

    INSERT INTO procedure_audit_log(procedure_name, execution_start, execution_end, status, error_message)
    VALUES ('sp_sample_procedure_with_audit', start_time, end_time, exec_status, err_msg);

    RETURN exec_status;
END;
$$;
```

- Logs start and end timestamps

- Captures success or failure status

- Saves error message if any exception occurs

- Inserts audit data into procedure_audit_log table
