# Using JSON and Semi-Structured Data in Snowflake SQL

---

## Objectives

- Understand how Snowflake handles semi-structured data (JSON, XML, AVRO, PARQUET)
- Learn to work with **VARIANT** data type
- Parse and query JSON using:
  - Dot notation (`data:key`)
  - Bracket notation (`data['key']`)
  - `:key::type` for type casting
- Use `FLATTEN()` to work with arrays inside JSON

---

##  Prerequisites

Let’s create a table with JSON data:

```sql
CREATE OR REPLACE TABLE user_data (
    id INT,
    data VARIANT
);

INSERT INTO user_data VALUES
(1, PARSE_JSON('{
  "name": "Alice",
  "age": 30,
  "location": { "city": "New York", "state": "NY" },
  "hobbies": ["reading", "traveling"]
}')),
(2, PARSE_JSON('{
  "name": "Bob",
  "age": 25,
  "location": { "city": "Los Angeles", "state": "CA" },
  "hobbies": ["gaming", "hiking"]
}'));
```

### Access JSON Elements
You can use dot or bracket notation to extract values from VARIANT:

```sql
SELECT
  id,
  data:name AS name,
  data:age AS age,
  data:location.city AS city
FROM user_data;

```

Use :: to cast to specific types:

```sql
SELECT
  id,
  data:name::STRING AS name,
  data:age::INT AS age
FROM user_data;
```

### FLATTEN() for Arrays
Use FLATTEN() to extract array elements:

```sql
SELECT
  id,
  hobby.value::STRING AS hobby
FROM user_data,
LATERAL FLATTEN(input => data:hobbies) AS hobby;
```

### Querying Nested and Dynamic Keys
Query nested fields and filter on them:

```sql
SELECT
  data:location.city::STRING AS city
FROM user_data
WHERE data:location.state = 'NY';
```

### Schema on Read
No need to define a schema beforehand. Snowflake lets you query JSON directly — this is called “schema on read.”

| Feature          | Usage Example                              | Notes                            |
| ---------------- | ------------------------------------------ | -------------------------------- |
| `VARIANT`        | Flexible type to hold semi-structured data | Works with JSON, XML, etc.       |
| Dot notation     | `data:name` or `data:location.city`        | Direct access                    |
| Bracket notation | `data['name']`                             | Use when keys have special chars |
| Type casting     | `data:age::INT`                            | Convert VARIANT to INT, STRING   |
| `FLATTEN()`      | Used with arrays                           | Unpacks array elements           |
| Schema on Read   | No schema needed upfront                   | Query structure as needed        |
