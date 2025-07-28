Develop a procedure that implements row-level security logic by filtering data based on user roles passed as input.

- You have a table called orders with a column region

- You want to allow users to only see data for regions assigned to their role

- You pass a role name (e.g., 'REGION_EAST') into the procedure

- A mapping table role_region_map(role_name, region) links roles to regions

```sql
CREATE OR REPLACE PROCEDURE sp_get_filtered_orders_by_role(user_role STRING)
RETURNS TABLE (order_id NUMBER, region STRING, customer_id STRING, amount NUMBER)
LANGUAGE SQL
AS
$$
DECLARE
  sql_text STRING;
BEGIN
  sql_text := '
    SELECT o.order_id, o.region, o.customer_id, o.amount
    FROM orders o
    JOIN role_region_map rrm
      ON o.region = rrm.region
    WHERE rrm.role_name = ' || QUOTE_LITERAL(user_role);

  RETURN TABLE(EXECUTE IMMEDIATE :sql_text);
END;
$$;
```

### Example Call:

```sql
CALL sp_get_filtered_orders_by_role('REGION_WEST');
```

- Allows custom filtering of rows by role, enforcing data security in logic

- Can be used as a building block for custom row-level security patterns

- This is not native RLS, but a logical enforcement in the procedure layer

- You could extend this further with session context for automatic role detection
