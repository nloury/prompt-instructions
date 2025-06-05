# Core Guidelines

## Environment
- Windows
- Conda venv for Python code
- Powershell or conda powershell for shell commands

## Project Architecture
- **Framework**: Python with PostgreSQL backend
- **Deployment**: Production-ready code expected on first submission

## Code Accuracy
- Do exactly what I ask. Do not rename variables or output names or columns that are either explicitly provided or provided by database schema
- Err on the side of caution and do not make assumptions about what I want. Ask for clarification if you are not sure.
- Always write correct, up to date, bug free, fully functional and working, secure, performant and efficient code.
- Conduct a final review to confirm the code is complete and meets all requirements. The solution should be ready for deployment without the need for further adjustments.
- Validate all user inputs before processing
- Handle edge cases explicitly (empty lists, None values, etc.)

## DRY (Don't Repeat Yourself)
- Extract repeated code into reusable functions
- Share common logic through proper abstraction
- Maintain single sources of truth
- Make use of base classes and inheritance to avoid code duplication

## Preserve Existing Code
- Keep unrelated code or functionalities. Pay attention to preserving existing structures.
- If I have existing code that you need to modify, do not change the structure unless explicitly asked to do so.

## Clean Structure
- Keep related code together
- Organize code in a logical hierarchy
- Use consistent file and folder naming conventions

## Encapsulation
- Hide implementation details
- Expose clear interfaces
- Move nested conditionals into well-named functions

## Code Quality Maintenance
- Refactor continuously
- Fix technical debt early

## Communication Style and Tone
- If I tell you that you are wrong, think about whether or not you think that's true and respond with facts.
- Be accurate without worrying about hurting my feelings if I am wrong or misguided.
- When errors occur, focus on providing solutions and clear explanations of what went wrong and why
- It is not necessary to agree with the user with statements such as "You're right" or "Yes".
- Prefer concise answers. Please avoid hyperbole and excitement, stick to the task at hand and complete it pragmatically.
- When fixing errors, explain the root cause and prevention strategy
- If I change code that you have written, do not take it personally. Please do not undo my changes without asking me first.

## Performance & Optimization
- Always think about how to optimize the code for speed, efficiency and maintainability.
- Make sure to suggest concise, efficient solutions that utilize vectorized over loops solutions wherever possible
- Favor clear and understandable code, addressing any performance trade-offs with reasoned explanations
- If choosing a less efficient algorithm for clarity, explain why this choice benefits maintainability and understanding.
- Make sure to manage memory usage and avoid unnecessary copies of data.
- Store values as variables or use helper functions to avoid repeated calculations or operations that can be done once and reused.

## Constants Over Magic Numbers
- Replace hard-coded values with named constants
- Use descriptive constant names that explain the value's purpose
- Keep constants at the top of the file or in a dedicated constants file

## Threading
- To improve performance, use threading where possible.
- Always leverage {project}.utils.threading module for threading operations. If you cannot find {project}.utils.threading then ask me to clarify.

```python
from {project}.utils.threading import threading

def process_data(self):
    """Example function to demonstrate threading usage"""
    def func(model, source_name: str):
        """Sub function to pass to threader"""
        data = fetch_data(model=model)  # Example caller
        return {'source_name': source_name, 'data': data}

    model_dict = {'source1': 'Model1', 'source2': 'Model2'}  # Example model dictionary
    inputs = [{'model': model, 'source_name': source_name} for source_name, model in model_dict.items()]
    result = threading.thread_function(target_fx=func,
                                       function_inputs=inputs,
                                       threaded=self._threaded,
                                       max_threads=self._max_threads,
                                       return_results=True,
                                       logger=self._logger
                                       )
    # any futher processing of result, like setting accessor key
    dict_key = 'internal_id'  # or other key that uniquely identifies the result
    return {r[dict_key]: r for r in result if r is not None}

class Base:
    # Some reusable code

class Derived(Base):
    # Extension of base class for specific functionality
```

## Web scraping
- Use {project}.utils.web.throttle_requests.py to avoid exceeding site limits
- Use {project}.utils.web.loading.py to handle most requests (i.e. non API) website requests (navigating to URL, retrying, etc)
- Where loading javascript, etc is important, leverage {project}.utils.web.playwright.
- It is ok to use direct requests.post, requests.get for API queries

```python
from {project}.utils.web.load import request_url, throttle_requests

url = 'http://example.com'
logger = CustomLogging('example_logger', level='debug_verbose')
throttle = throttling.ThrottleRequests(site_ref='example_site', logger=logger, url=url)
response = request_url(url=url,
                       method='GET',
                       data=payload,
                       headers={'User-Agent': 'my-app'},
                       max_attemps=4,
                       timeout=10,
                       logger=logger)
```

## Output Formatting
- Use , separators for thousands
- Use () for negative numbers
- Use - for 0