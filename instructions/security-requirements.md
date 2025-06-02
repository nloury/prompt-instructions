# Security Requirements

## Input Validation
- Always validate and sanitize user inputs
- Use parameterized queries only - never string concatenation for SQL
- Implement proper error handling that doesn't expose system internals
- Log security events (failed auth, suspicious inputs) at appropriate levels
- Follow principle of least privilege for database connections
- Never expose credentials. Authentication will leverage a .env file to point to securely stored credentials. If you can't find the .env please ask me to clarify.

## Validation Examples
```python
def validate_user_input(user_id: int, email: str) -> dict:
    """Validate user inputs with proper error handling."""
    errors = []
    
    if not isinstance(user_id, int) or user_id <= 0:
        errors.append("User ID must be a positive integer")
    
    if not re.match(r'^[^@]+@[^@]+\.[^@]+$', email):
        errors.append("Invalid email format")
    
    if errors:
        raise ValueError(f"Validation failed: {'; '.join(errors)}")
    
    return {"user_id": user_id, "email": email.lower().strip()}
```

## Secure Database Patterns
```python
# CORRECT: Parameterized query
execution_dict = {
    'sql': '''
        SELECT id, name, email 
        FROM main.users 
        WHERE status = %(status)s 
        AND created_date >= %(start_date)s
    ''',
    'values': {
        'status': 'active',
        'start_date': '2024-01-01'
    }
}

# WRONG: String concatenation (never do this)
# sql = f"SELECT * FROM users WHERE id = {user_id}"  # SQL injection risk!
```

## Error Handling Security
- Never expose internal system details in error messages
- Log detailed errors internally, return generic messages to users
- Use structured exception types for different error categories

```python
try:
    sensitive_operation()
except DatabaseConnectionError:
    # Log detailed error internally
    logger.error(f"DB connection failed: {detailed_error}")
    # Return generic message to user
    raise APIError("Service temporarily unavailable")
```

## What NOT to Log
- Passwords, tokens, API keys
- Personal Identifiable Information (PII)
- Credit card numbers or financial data
- Full stack traces in production logs