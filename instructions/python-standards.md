# Python Standards

## Type Annotations
- Use explicit typing wherever possible
- Use build in types, do not use `typing` module unless necessary (dict[str] is ok, but not typing.Dict[str])
- Use | for union types (e.g., `str | None`)
- List explicit types accepted instead of typing.Any unless absolutely necessary. If using Any, please call this out with a rationale for doing so.
- Optional can be inferred from the default value of None, so avoid using Optional
- Use single quotes (') unless double quote is necessary
- Prefer fstrings for string formatting and concatenation

```python
# Explicit typing using built in types
def example(name: str, age: int | None = None) -> dict[str, str | int]:
    """Use this typing style"""
    return {'name': name, 'age': age}

# Avoid using the typing module unless necessary
from typing import Union, Optional, Dict
def example(name: Union[str, None], age: Optional[int] = None) -> Dict[str, Union[str, int]]:
    """Do not use this typing style"""
    return {'name': name, 'age': age}

# Prefer single quotes
print('Use single quotes for strings')  # Do this
print(f"Use double quotes when {test_dict['key']} necessary")  # This is ok
print("Unnecessary double quotes")  # Avoid this

# Use fstrings for string formatting
example_str = 'process'
f'{example_str} complete'  # Do this
example_str + ' complete'  # Avoid this
```

## Naming Conventions
- Classes: PascalCase (e.g., `DataProcessor`)
- Functions/variables: snake_case (e.g., `process_data`)
- Constants: UPPER_SNAKE_CASE (e.g., `MAX_RETRY_COUNT`)
- Private methods: prefix with single underscore (`_helper_method`)
- Avoid long variable names with more than 1 (or at most 2) underscores

## Documentation
- Always use doc strings to clarify the purpose of functions and classes
- Concise and clear docstrings. For simple / transparent functions and classes, do not need to specify args, returns, raises, etc
- Use comments to describe what blocks of code are doing and the purpose
- Include type hints in docstrings for complex return types
- Document API endpoints with request/response examples
- Add inline comments for business logic decisions

## Error Handling Patterns
```python
# Preferred error handling pattern
try:
    result = risky_operation()
    self._logger.add_log(base_message=f'Operation successful: {result}', level='info')
    return result
except SpecificException as e:
    self._logger.add_log(
        base_message=f'Expected error in operation: {str(e)}',
        level='warning',
        caller_depth=0
    )
    return default_value
except Exception as e:
    self._logger.add_log(
        base_message=f'Unexpected error in operation: {str(e)}',
        level='error',
        caller_depth=0
    )
    raise ProcessingError(f"Operation failed: {str(e)}") from e
```

## Class Structure
- Avoid relying on **kwargs or **args unless absolutely necessary. Use explicit parameters instead.
- Prefer private methods like self._ unless necessary to expose methods publicly.
- Create base classes where that can help avoid code duplication and improve maintainability.

```python
from {project}.utils.log import CustomLogging

class ExampleClass:
    """Descriptive docstring"""  # prefer single line docstrings for simple classes
    def __init__(self,
                 explicit_variables: dict[str],  # use type hints for clarity
                 logger: CustomLogging = None
                 ):
        self._logger = logger if logger else CustomLogging('ExampleClass', level='debug_verbose')
        self._logger.add_log(override_message='Initializing ExampleClass', level='debug_verbose')
        
        # assign variables
        # add other variables to be set by the class

     # properties
     # functions

# Create base classes where that can help avoid code duplication and improve maintainability.
class BaseProcessor:
    def process(self, data):
        raise NotImplementedError("Subclasses must implement this method")

class CustomProcessor(BaseProcessor):
    def process(self, data):
        # Custom processing logic
        pass
```

## Flexible Data Structures
- Use optional arguments, default values, and/or boolean flags to allow flexibility in function calls.
- With flexible functions, make sure to process the arguments efficiently and avoid unnecessary duplication of calls.
- For complex sets of arguments, use a config class to encapsulate the parameters.
- Use dataclasses for config class to improve readability and maintainability.

```python
# Flexible function example
def fetch_data(source: str, limit: int = 100, include_metadata: bool = False) -> dict:
    """Fetch data from a specified source with optional parameters."""
    out = {'data': fetch_from_source(source, limit)}
    if include_metadata:
        # Assume get_metadata is a function that fetches metadata for the source
        out['metadata'] = get_metadata(source)
    return out

# Config example
from dataclasses import dataclass, replace

@dataclass
class ExampleConfig:
    """Descriptive docstring"""
    self.source: str = source
    self.model_type: str = 'linear'
    # other parameters can be added as needed
    
    def __post_init__(self):
        # Process data, validate inputs, or set defaults, etc
        
    def _load_file(self):
        # Load configuration from a file or other source

    # for frequently tweaked parameters, use replace to create a new instance with updated values to avoid having to respecify all parameters
    def swap_model(self,  new_model_type: str):
        """Create a new instance with updated model type"""
        return replace(self, model_type=new_model_type) 

def initialize_service(config: FetchConfig):
    source = config.source
    limit = config.limit
    # Initialize service based on config
```

## Preferred Formatting
- Prefer passing variables explicitly to avoid issues with reordering or missing parameters.
- Handle None values concisely and clearly

```python
# dicts with more than 2 items should be formatted like this
my_dict = {
    'key1': 'value1',
    'key2': 'value2',
}

# Explicit passing of variables
call_function(first=1, second=2, third=3, fourth=4)  # Do this
call_function(1, 2, 3, 4)  # Avoid this > Hard to read and maintain

# None handling
def test(opt=None):
    value = opt if opt else {default_value}
```

## Anti-Patterns to Avoid
- Hardcoded credentials or connection strings
- Catching generic Exception without re-raising
- Missing input validation on public methods
- Using string formatting for SQL queries
- Missing type hints on public interfaces