# Testing & Logging

## Testing Requirements
- No need to create unit tests unless asked for. If you are asked to create unit tests, please include edge case testing (empty inputs, boundary values)
- Use descriptive test names: `test_validate_user_input_with_invalid_email_raises_value_error`

### Test Structure
```python
import pytest
from unittest.mock import Mock, patch

class TestExampleClass:
    def test_initialization_with_valid_data_succeeds(self):
        """Test that class initializes correctly with valid data."""
        valid_data = {"key": "value"}
        instance = ExampleClass(valid_data)
        assert instance.is_ready is True
    
    def test_initialization_with_empty_data_raises_value_error(self):
        """Test that empty data raises appropriate error."""
        with pytest.raises(ValueError, match="explicit_variables cannot be empty"):
            ExampleClass({})
```

## Logging Standards
- Always use utils.log CustomLogging for logging
- If you cannot find the utils.log file then ask
- Pass logger as an argument to use same log through workflow for better traceability

### Logger Setup
```python
# Instance logger
logger = CustomLogging('{logger_file_name}')  # no need to specify level when initializing

# Class logger
self._logger = logger if logger else CustomLogging('{logger_file_name}')
```

### Logging Patterns
```python
# Intialize logger
def test(logger: CustomLogging=None):
    logger = logger if logger else CustomLogging('{logger_file_name}')  # do this
    logger = logger if logger else CustomLogging('{logger_file_name}', level='debug_verbose')  # never include level in the intialization
    
# Starting processes
self._logger.add_log(override_message='Starting {process message}', level='debug_verbose')

# Regular logging
self._logger.add_log(base_message={log_message}, level={level})

# Error logging with caller depth
def primary_function():
    try:
        # some code
        pass
    except Exception as e:
        self._error_handling_function(e)

def _error_handling_function(self, error: Exception):
    self._logger.add_log(
        base_message=f'Error in primary_function: {str(error)}', 
        level='error', 
        caller_depth=1
    )
```

### When to Use override_message vs base_message
- **override_message**: When caller is not important (starting processes, initialization)
- **base_message**: When caller context matters (errors, business logic events)

## Code Review Checklist
- Security: All inputs validated, no SQL injection risks
- Performance: Efficient algorithms, proper indexing considered
- Maintainability: Clear naming, adequate documentation
- Testing: Unit tests present, edge cases covered
- Logging: Appropriate log levels, sensitive data not logged
- Error handling: Graceful failure, proper exception types