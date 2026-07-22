# Python Interview Master Guide
# Module 03 – Decorators (Questions 26–35)

---

# Introduction

A **decorator** is a callable that takes another function (or class), extends or modifies its behavior, and returns a new callable **without modifying the original source code**.

Internally, decorators are implemented using **higher-order functions** and **closures**.

```python
@decorator
def greet():
    pass
```

is equivalent to

```python
def greet():
    pass

greet = decorator(greet)
```

---

# Q26. Implement a decorator from scratch.

## Basic Decorator

```python
def logger(func):

    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print("Execution completed")
        return result

    return wrapper


@logger
def add(a, b):
    return a + b

print(add(10, 20))
```

Output

```
Calling add
Execution completed
30
```

---

## Memory Diagram

```
add()

↓

logger(add)

↓

wrapper()

↓

Closure

↓

Original add()
```

---

## Internal Flow

```
Function Definition

↓

Decorator Called

↓

Wrapper Created

↓

Wrapper Returned

↓

Original Function Replaced
```

---

## Interview Follow-up

**Q:** Does the original function still exist?

Yes.

The wrapper keeps a reference to it through a closure.

---

# Q27. How do parameterized decorators work?

A parameterized decorator is a decorator that accepts arguments.

Example

```python
def repeat(times):

    def decorator(func):

        def wrapper(*args, **kwargs):

            for _ in range(times):
                func(*args, **kwargs)

        return wrapper

    return decorator


@repeat(3)
def greet():
    print("Hello")

greet()
```

Output

```
Hello
Hello
Hello
```

---

## Internal Execution

```
@repeat(3)

↓

repeat(3)

↓

decorator()

↓

wrapper()

↓

greet()
```

---

## Memory Diagram

```
repeat(3)

↓

Closure

↓

times = 3

↓

decorator

↓

wrapper
```

---

## Why Nested Functions?

There are three levels:

```
repeat()

↓

decorator()

↓

wrapper()
```

- Level 1 stores decorator arguments.
- Level 2 receives the function.
- Level 3 executes the function.

---

# Q28. Why use functools.wraps?

Without `functools.wraps`, metadata from the original function is lost.

Example

```python
def logger(func):

    def wrapper():
        return func()

    return wrapper


@logger
def greet():
    """Greeting Function"""
    print("Hello")

print(greet.__name__)
```

Output

```
wrapper
```

The original function name is lost.

---

## Correct Implementation

```python
from functools import wraps

def logger(func):

    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)

    return wrapper
```

Now

```python
print(greet.__name__)
print(greet.__doc__)
```

Output

```
greet

Greeting Function
```

---

## What wraps Copies

- `__name__`
- `__doc__`
- `__module__`
- `__annotations__`
- `__dict__`
- `__wrapped__`

---

## Interview Question

**Why is `__wrapped__` useful?**

Frameworks like Flask, FastAPI, Django, and introspection tools use it to access the original function.

---

# Q29. Decorator execution order.

Suppose

```python
@A
@B
@C
def func():
    pass
```

Equivalent to

```python
func = A(B(C(func)))
```

---

## Execution Order

Decoration

```
C

↓

B

↓

A
```

Runtime

```
A

↓

B

↓

C

↓

Original Function

↓

C

↓

B

↓

A
```

---

## Example

```python
def A(func):

    def wrapper():
        print("A Before")
        func()
        print("A After")

    return wrapper


def B(func):

    def wrapper():
        print("B Before")
        func()
        print("B After")

    return wrapper


@A
@B
def hello():
    print("Hello")

hello()
```

Output

```
A Before
B Before
Hello
B After
A After
```

---

# Q30. Class decorators vs function decorators.

## Function Decorator

```python
def logger(func):

    def wrapper(*args, **kwargs):
        print("Logging")
        return func(*args, **kwargs)

    return wrapper
```

---

## Class Decorator

```python
class Logger:

    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        print("Logging")
        return self.func(*args, **kwargs)


@Logger
def greet():
    print("Hello")

greet()
```

---

## Comparison

| Feature | Function Decorator | Class Decorator |
|----------|-------------------|----------------|
| Simplicity | Easy | Moderate |
| Stateful | Limited | Excellent |
| Inheritance | No | Yes |
| Uses Closures | Yes | No |
| Best For | Simple wrappers | Complex logic |

---

## When to Use Class Decorators

- Configuration
- Stateful decorators
- Caching
- Metrics collection

---

# Q31. Decorator chaining.

Multiple decorators can be applied together.

Example

```python
from functools import wraps

def uppercase(func):

    @wraps(func)
    def wrapper():
        return func().upper()

    return wrapper


def exclaim(func):

    @wraps(func)
    def wrapper():
        return func() + "!"

    return wrapper


@exclaim
@uppercase
def greet():
    return "hello"

print(greet())
```

Output

```
HELLO!
```

---

## Flow

```
greet

↓

uppercase

↓

exclaim

↓

Result
```

Equivalent

```python
greet = exclaim(uppercase(greet))
```

---

# Q32. Authentication decorator implementation.

Example

```python
from functools import wraps

CURRENT_USER = {
    "username": "monika",
    "authenticated": True
}


def login_required(func):

    @wraps(func)
    def wrapper(*args, **kwargs):

        if not CURRENT_USER["authenticated"]:
            raise PermissionError("Login required")

        return func(*args, **kwargs)

    return wrapper


@login_required
def dashboard():
    return "Welcome"

print(dashboard())
```

Output

```
Welcome
```

---

## Real-world Usage

- Django
- Flask
- FastAPI
- REST APIs
- JWT authentication

---

## Flow

```
Request

↓

Authentication Decorator

↓

Token Validation

↓

Call API

↓

Return Response
```

---

# Q33. Retry decorator with exponential backoff.

Useful for APIs, databases, cloud services, and distributed systems.

Example

```python
import time
from functools import wraps

def retry(max_attempts=3, delay=1):

    def decorator(func):

        @wraps(func)
        def wrapper(*args, **kwargs):

            current_delay = delay

            for attempt in range(max_attempts):

                try:
                    return func(*args, **kwargs)

                except Exception:

                    if attempt == max_attempts - 1:
                        raise

                    time.sleep(current_delay)
                    current_delay *= 2

        return wrapper

    return decorator
```

Usage

```python
@retry(max_attempts=4)
def fetch_data():
    ...
```

---

## Delay Pattern

```
1 sec

↓

2 sec

↓

4 sec

↓

8 sec
```

---

## Common Uses

- HTTP requests
- AWS SDK
- Azure SDK
- Database reconnect
- Kafka
- RabbitMQ

---

# Q34. Timing decorator.

Measures execution time.

```python
import time
from functools import wraps

def timer(func):

    @wraps(func)
    def wrapper(*args, **kwargs):

        start = time.perf_counter()

        result = func(*args, **kwargs)

        end = time.perf_counter()

        print(f"{func.__name__} took {end-start:.6f} seconds")

        return result

    return wrapper
```

Example

```python
@timer
def slow():

    time.sleep(2)

slow()
```

Output

```
slow took 2.000xxx seconds
```

---

## Why `perf_counter()`?

It provides the highest available timer resolution and is suitable for benchmarking.

---

## Real-world Uses

- API latency
- Database queries
- Machine learning inference
- Performance profiling

---

# Q35. Memoization decorator.

Memoization stores the result of expensive function calls.

Simple implementation

```python
from functools import wraps

def memoize(func):

    cache = {}

    @wraps(func)
    def wrapper(*args):

        if args in cache:
            return cache[args]

        result = func(*args)

        cache[args] = result

        return result

    return wrapper
```

---

## Example

```python
@memoize
def square(n):

    print("Calculating")

    return n * n

print(square(5))
print(square(5))
```

Output

```
Calculating
25
25
```

The second call retrieves the value from the cache.

---

## Memory Diagram

```
Cache Dictionary

{

(5):25,

(10):100

}
```

---

## Memoization vs lru_cache

| Feature | Memoization | lru_cache |
|----------|-------------|-----------|
| Built-in | No | Yes |
| Eviction | No | Yes |
| Thread-safe | No | Yes |
| Size Limit | Unlimited | Configurable |
| Production Ready | Usually No | Yes |

---

# Best Practices for Writing Decorators

- Always use `functools.wraps`.
- Support `*args` and `**kwargs` for flexibility.
- Keep decorators focused on a single responsibility.
- Avoid modifying function arguments unless necessary.
- Preserve return values and exceptions.
- Write parameterized decorators for configurable behavior.

---

# Common Interview Follow-up Questions

1. What happens if a decorator does not return the wrapper?
2. Why should `functools.wraps` be used in almost every decorator?
3. How do decorators work with class methods and static methods?
4. Can decorators modify function arguments?
5. How can multiple decorators affect execution order?
6. What is the difference between a decorator and a context manager?
7. How would you implement a thread-safe memoization decorator?
8. How do decorators interact with async functions (`async def`)?
9. How are decorators used in frameworks like Django and FastAPI?
10. Can a decorator change a function's signature?




---

# Module Summary

Topics Covered

- Implementing decorators from scratch
- Parameterized decorators
- `functools.wraps`
- Decorator execution order
- Class decorators
- Function decorators
- Decorator chaining
- Authentication decorators
- Retry decorators with exponential backoff
- Timing decorators
- Memoization decorators



# Python Interview Master Guide
# Module 03A – Advanced Decorator Interview Questions

These follow-up questions are commonly asked in senior Python interviews (Google, Meta, Amazon, Microsoft, Apple, Uber, Stripe, etc.). They focus on understanding **how decorators work internally**, **CPython behavior**, and **real-world applications**.

---

# Q1. What happens if a decorator does not return the wrapper?

## Short Answer

If a decorator does **not** return the wrapper (or another callable), the decorated function becomes `None` (or whatever the decorator returns), making it **unusable**.

---

## Example

```python
def logger(func):

    def wrapper():
        print("Before")
        func()
        print("After")

    # Forgot to return wrapper


@logger
def greet():
    print("Hello")

greet()
```

Output

```
TypeError: 'NoneType' object is not callable
```

---

## Why?

Python transforms:

```python
@logger
def greet():
    pass
```

into

```python
greet = logger(greet)
```

Since `logger()` returns `None`, it becomes:

```python
greet = None
```

Calling:

```python
greet()
```

becomes

```python
None()
```

which raises:

```
TypeError
```

---

## Correct Version

```python
def logger(func):

    def wrapper():
        print("Before")
        func()
        print("After")

    return wrapper
```

---

## Interview Tip

A decorator **must return a callable**.

Usually that callable is:

- wrapper function
- partial function
- callable class

---

# Q2. Why should `functools.wraps` be used in almost every decorator?

## Short Answer

Without `functools.wraps`, Python loses metadata of the original function.

---

## Example

```python
def logger(func):

    def wrapper():
        return func()

    return wrapper


@logger
def greet():
    """Greeting Function"""
    pass

print(greet.__name__)
print(greet.__doc__)
```

Output

```
wrapper
None
```

Original information is lost.

---

## Correct Version

```python
from functools import wraps

def logger(func):

    @wraps(func)
    def wrapper():
        return func()

    return wrapper
```

Now

```
greet.__name__

↓

greet
```

---

## What `wraps` Copies

- `__name__`
- `__doc__`
- `__module__`
- `__annotations__`
- `__qualname__`
- `__dict__`
- `__wrapped__`

---

## Why It Matters

Frameworks rely on metadata:

- FastAPI
- Flask
- Django
- Click
- pytest
- inspect module

Without `wraps`, introspection and documentation can break.

---

# Q3. How do decorators work with class methods and static methods?

Decorators can wrap:

- instance methods
- class methods
- static methods

---

## Instance Method

```python
from functools import wraps

def logger(func):

    @wraps(func)
    def wrapper(*args, **kwargs):
        print("Calling")
        return func(*args, **kwargs)

    return wrapper


class User:

    @logger
    def greet(self):
        print("Hello")

User().greet()
```

`self` is simply the first positional argument.

---

## Class Method

```python
class User:

    @classmethod
    @logger
    def info(cls):
        print(cls.__name__)
```

Execution order

```
logger

↓

classmethod
```

Equivalent

```python
info = classmethod(logger(info))
```

---

## Static Method

```python
class User:

    @staticmethod
    @logger
    def hello():
        print("Hello")
```

---

## Important Order

Correct

```python
@classmethod
@logger
```

is different from

```python
@logger
@classmethod
```

because decorators are applied from **bottom to top**.

---

## Interview Tip

Decorators receive whatever object the previous decorator returned.

Sometimes that object is a function.

Sometimes it's a `classmethod` object.

---

# Q4. Can decorators modify function arguments?

## Yes.

A decorator can inspect, validate, modify, or replace arguments before calling the original function.

---

## Example

```python
from functools import wraps

def positive_only(func):

    @wraps(func)
    def wrapper(x):

        x = abs(x)

        return func(x)

    return wrapper


@positive_only
def square(x):
    return x * x

print(square(-5))
```

Output

```
25
```

---

## Validation Example

```python
def validate(func):

    @wraps(func)
    def wrapper(age):

        if age < 0:
            raise ValueError

        return func(age)

    return wrapper
```

---

## Common Uses

- Input validation
- Type conversion
- Sanitization
- Authentication
- Logging

---

# Q5. How can multiple decorators affect execution order?

Example

```python
@A
@B
@C
def hello():
    pass
```

Equivalent

```python
hello = A(B(C(hello)))
```

---

## Decoration Order

```
C

↓

B

↓

A
```

---

## Runtime Order

```
A Before

↓

B Before

↓

C Before

↓

Function

↓

C After

↓

B After

↓

A After
```

---

## Example

```python
@timer
@login_required
@logger
def dashboard():
    pass
```

Equivalent

```python
dashboard = timer(login_required(logger(dashboard)))
```

---

## Why It Matters

Changing decorator order can completely change application behavior.

Example

```
Caching before authentication

vs

Authentication before caching
```

These produce different results.

---

# Q6. What is the difference between a decorator and a context manager?

## Decorator

Wraps a function.

```python
@logger
def hello():
    pass
```

---

## Context Manager

Wraps a block of code.

```python
with open("file.txt") as f:
    print(f.read())
```

---

## Comparison

| Decorator | Context Manager |
|-----------|----------------|
| Wraps function | Wraps code block |
| Executes on function call | Executes on entering block |
| Uses closures/classes | Uses `__enter__` and `__exit__` |
| Permanent | Temporary |

---

## Context Manager Example

```python
from contextlib import contextmanager

@contextmanager
def timer():

    print("Start")

    yield

    print("End")
```

Usage

```python
with timer():
    print("Running")
```

---

## Interview Tip

Use

Decorator

→ Function behavior

Context Manager

→ Resource management

---

# Q7. How would you implement a thread-safe memoization decorator?

A normal dictionary is **not thread-safe** for concurrent writes.

---

## Solution

Use `threading.Lock`.

```python
from functools import wraps
from threading import Lock

def memoize(func):

    cache = {}

    lock = Lock()

    @wraps(func)
    def wrapper(*args):

        with lock:

            if args in cache:
                return cache[args]

        result = func(*args)

        with lock:
            cache[args] = result

        return result

    return wrapper
```

---

## Better Solution

Use Python's built-in

```python
functools.lru_cache
```

It already provides thread safety in CPython.

---

## Interview Question

Why use a lock?

Without it:

Two threads may compute the same expensive result simultaneously.

---

# Q8. How do decorators interact with async functions (`async def`)?

Normal decorators cannot simply call an async function.

---

## Wrong

```python
def logger(func):

    def wrapper():

        return func()
```

`func()` returns a coroutine.

---

## Correct

```python
from functools import wraps

def logger(func):

    @wraps(func)
    async def wrapper(*args, **kwargs):

        print("Before")

        result = await func(*args, **kwargs)

        print("After")

        return result

    return wrapper
```

---

## Example

```python
@logger
async def fetch():

    return "Done"
```

---

## Interview Tip

Decorators must preserve async behavior.

If decorating an async function:

Use

```python
async def wrapper()
```

and

```python
await func()
```

---

# Q9. How are decorators used in frameworks like Django and FastAPI?

Decorators are fundamental building blocks in modern Python frameworks.

---

## Django

Authentication

```python
@login_required
def dashboard(request):
    ...
```

Transaction

```python
@transaction.atomic
```

Permissions

```python
@permission_required(...)
```

Caching

```python
@cache_page(60)
```

---

## FastAPI

Routing

```python
@app.get("/users")
async def users():
    ...
```

Dependency Injection

```python
@router.post(...)
```

Middleware

```python
@app.middleware("http")
```

---

## Flask

```python
@app.route("/")
```

Registers a URL with a function.

---

## pytest

```python
@pytest.fixture
```

Creates reusable test fixtures.

---

## Internal Flow

```
HTTP Request

↓

Decorator

↓

Authentication

↓

Validation

↓

Business Logic

↓

Response
```

Decorators help separate cross-cutting concerns from business logic.

---

# Q10. Can a decorator change a function's signature?

## Yes.

A decorator can expose a different signature than the original function.

---

## Example

```python
from functools import wraps

def add_logging(func):

    @wraps(func)
    def wrapper(*args, **kwargs):

        print("Logging")

        return func(*args, **kwargs)

    return wrapper
```

The wrapper technically accepts:

```python
(*args, **kwargs)
```

instead of the original parameters.

---

## Preserving Signature

Libraries like

- `functools.wraps`
- `inspect`
- `decorator`
- `wrapt`

help preserve or emulate the original signature.

---

## Example

```python
import inspect

print(inspect.signature(add))
```

Without careful implementation, the signature may become:

```
(*args, **kwargs)
```

instead of

```
(a: int, b: int)
```

---

## Can a Decorator Intentionally Change It?

Yes.

Example

```python
def inject_user(func):

    def wrapper():

        user = "admin"

        return func(user)

    return wrapper
```

The decorated function now behaves as if it receives an injected argument.

---

# Summary

This module covered advanced decorator concepts:

- Decorator return values
- `functools.wraps`
- Decorating instance, class, and static methods
- Modifying function arguments
- Decorator execution order
- Decorators vs context managers
- Thread-safe memoization
- Decorating asynchronous functions
- Decorators in Django and FastAPI
- Function signature preservation and modification

These topics are frequently discussed in senior Python backend interviews because they demonstrate a deep understanding of Python's object model, function internals, and practical software design.

