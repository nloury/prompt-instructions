# Database Standards

## Connection Rules
- Always interact with the database using {package}.utils.db module
- If you can't find utils.db then ask me to clarify
- Use PostgreSQL unless otherwise specified
- Always use connection pooling for production code
- Implement proper transaction rollback on errors
- Use read replicas for heavy analytical queries when available

## Query Standards
- Always capitalize operators like SELECT, FROM, CASE
- Always explicitly reference schema. If unsure of schema please clarify.
- Always use an alias when querying more than 1 table
- Use batch writes when possible
- Make sure to write all related queries as transactions by passing a list of execution_dicts to write_sql or batch_sql
- When using queries to load data, use the column name after loaded (for the variable name or for output columns). If you think it should be renamed then ask me.
- When passing data/values to query, prefer explicitly named values and passing a dict instead of implicit %s
- Use CTE tables for complex queries to improve readability and maintainability (e.g. avoid retyping/recalculating intermediate values)

## Usage Examples

### Writing to Database
```python
from {project}.utils.db import write_sql, batch_sql

# Single execution
execution_dict = {
    'sql': 'INSERT INTO main.table_name (col1, col2) VALUES (%(example1)s, %(example2)s)',
    'values': {'example1': ['value1', 'value2'], 'example2': ['other1', 'other2']}
}
write_sql(execution_dict)

# Multiple executions
execution_list = [execution_dict1, execution_dict2]
write_sql(execution_list)

# Batch writing
batch_list = [execution_list1, execution_list2]
batch_sql(batch_list=batch_list)

# Creating insert statement
from {project}.utils.db.format import format_insert

value_list = get_value_list(df)  # get list of dicts to insert
sql_insert = {
    'sql': format_insert(schema='master',
                         table_name='box_scores',
                         value_dict=value_list[0],  # pass first dict to extract column names
                         conflict_columns=['game_id', 'team_id', 'player_id'],
                         conflict_action='NOTHING'
                         )
    , 'values': value_list
    , 'error_ref': f'compiling box scores'
}
db_execution_list.append(sql_insert)
```

### Reading from Database
```python
from {project}.utils.db.read import query_sql

sql_query = """
    SELECT
        id,
        name,
        CASE
            WHEN tbl.reference_date > NOW() THEN 'future'
            ELSE 'past'
        END as time_period
    FROM main.table as tbl
    LEFT JOIN main.other_table as other ON other.id = tbl.id
    WHERE tbl.status = %(status)s
"""
result = query_sql(sql_query, values={'status': 'active'}, return_dict=True)
# return_dict=True ensures results are returned as a dict, False returns a list of tuples (orient controls the output format)
# fetch one=True will return a single value
# single_col=True will return a single column as a list

```

## Transaction Patterns
```python
# All related operations in one transaction
execution_list = [
    {
        'sql': 'UPDATE main.users SET last_login = %(timestamp)s WHERE id = %(user_id)s',
        'values': {'timestamp': datetime.now(), 'user_id': 123}
    },
    {
        'sql': 'INSERT INTO main.audit_log (user_id, action) VALUES (%(user_id)s, %(action)s)',
        'values': {'user_id': 123, 'action': 'login'}
    }
]
write_sql(execution_list)  # Single transaction
```