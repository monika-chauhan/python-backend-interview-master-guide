# Python Interview Master Guide
# Module 02 – Functions & Closures (Questions 16–25)

---

# Q16. How are functions first-class objects?

## Definition

In Python, **functions are first-class objects**, meaning they are treated like any other object (such as integers, strings, or lists). They can be:

- Assigned to variables
- Passed as arguments
- Returned from other functions
- Stored in data structures
- Created at runtime

Internally, a Python function is represented by a **PyFunctionObject** in CPython.

---

## Example 1: Assigning a Function

```python
def greet():
    return "Hello"

say_hello = greet

print(say_hello())
```

**Output**

```
Hello
```

Memory Diagram

```
greet --------+
              |
say_hello ----|-------> Function Object
                       |
                       +--> Code Object
                       +--> Globals
                       +--> Defaults
                       +--> Closure
```

Both variables point to the **same function object**.

---

## Example 2: Passing Functions

```python
def add(a, b):
    return a + b

def calculate(func, x, y):
    return func(x, y)

print(calculate(add, 5, 10))
```

Output

```
15
```

---

## Example 3: Returning Functions

```python
def outer():

    def inner():
        return "Inside"

    return inner

f = outer()

print(f())
```

Output

```
Inside
```

---

## CPython Internal Structure

A function object contains:

- Code object
- Global namespace
- Default arguments
- Closure variables
- Function annotations
- Documentation string

Simplified structure

```c
PyFunctionObject
    |
    +-- Code Object
    +-- Globals
    +-- Defaults
    +-- Closure
    +-- __dict__
```

---

## Interview Follow-up

**Q:** Why are functions considered objects?

**Answer:**

Because they have:
- Identity
- Type
- Attributes
- Memory address
- Can be referenced like any other object

---

# Q17. Explain closures with memory diagrams.

## Definition

A closure is a function that remembers variables from its enclosing scope even after the outer function has finished execution.

---

## Example

```python
def outer():

    x = 10

    def inner():
        return x

    return inner

func = outer()

print(func())
```

Output

```
10
```

---

## Memory Flow

Before returning

```
outer()

Local Variables

x = 10

inner
```

After returning

```
func
 |
 |
 v

Function Object
     |
     +------ Closure Cell
                    |
                    v
                    10
```

The local variable survives because the closure holds a reference.

---

## Inspecting Closure

```python
def outer():

    x = 50

    def inner():
        return x

    return inner

f = outer()

print(f.__closure__)
```

Output

```
(<cell at ...>,)
```

Access value

```python
print(f.__closure__[0].cell_contents)
```

Output

```
50
```

---

## Real-world Example

```python
def multiplier(n):

    def multiply(x):
        return x * n

    return multiply

double = multiplier(2)

triple = multiplier(3)

print(double(5))
print(triple(5))
```

Output

```
10
15
```

---

## Advantages

- Data hiding
- Stateful functions
- Callback functions
- Function factories

---

# Q18. How do free variables work?

## Definition

A free variable is a variable used inside a function but defined in an enclosing scope.

Example

```python
def outer():

    message = "Python"

    def inner():
        print(message)

    return inner
```

Here,

```
message
```

is a free variable.

---

## Variable Resolution

```
outer()

message

↓

inner()

uses message
```

---

## CPython Implementation

Free variables are stored in **cell objects**.

```
Cell Object

↓

message

↓

"Python"
```

The function stores references instead of copying values.

---

## Inspect Free Variables

```python
def outer():

    x = 100

    def inner():
        return x

    return inner

print(outer().__code__.co_freevars)
```

Output

```
('x',)
```

---

## Interview Question

**Why not copy the value?**

Because closures must reflect updated values if they change.

---

# Q19. Difference between closure and decorator.

| Feature | Closure | Decorator |
|----------|----------|-----------|
| Purpose | Remember variables | Modify function behavior |
| Returns | Function | Wrapped function |
| Uses free variables | Yes | Usually |
| Syntax | Normal function | @decorator |

---

Closure Example

```python
def power(n):

    def calculate(x):
        return x ** n

    return calculate

square = power(2)

print(square(5))
```

---

Decorator Example

```python
def logger(func):

    def wrapper():

        print("Calling function")

        func()

    return wrapper


@logger
def greet():
    print("Hello")

greet()
```

Output

```
Calling function
Hello
```

---

Relationship

```
Decorator

↓

Uses Closure

↓

Returns Wrapper
```

Most decorators internally rely on closures.

---

# Q20. Explain function annotations.

Function annotations provide metadata about parameters and return values.

---

Example

```python
def add(a: int, b: int) -> int:
    return a + b
```

---

Access annotations

```python
print(add.__annotations__)
```

Output

```python
{
    'a': int,
    'b': int,
    'return': int
}
```

---

Important

Python **does not enforce** annotations.

```python
print(add("10", "20"))
```

Output

```
1020
```

No error is raised.

---

Common Uses

- Type checking
- IDE support
- FastAPI
- Pydantic
- Static analysis
- Documentation

---

# Q21. Mutable default arguments—why do they happen?

This is one of Python's most frequently asked interview questions.

---

Problem

```python
def append_item(item, items=[]):

    items.append(item)

    return items

print(append_item(1))
print(append_item(2))
```

Output

```
[1]
[1, 2]
```

Most people expect

```
[2]
```

---

Why?

Default arguments are evaluated **once**, when the function is created—not each time it is called.

Memory

```
Function Object

↓

Defaults Tuple

↓

[]
```

Every call shares the same list.

---

Correct Solution

```python
def append_item(item, items=None):

    if items is None:
        items = []

    items.append(item)

    return items
```

---

Interview Tip

Use `None` as the default value for mutable objects.

---

# Q22. Explain *args and **kwargs internally.

## *args

Collects extra positional arguments into a tuple.

Example

```python
def total(*args):

    return sum(args)

print(total(1,2,3))
```

Output

```
6
```

Memory

```
args

↓

(1,2,3)
```

---

## **kwargs

Collects keyword arguments into a dictionary.

```python
def details(**kwargs):

    print(kwargs)

details(name="Monika", age=25)
```

Output

```
{
    'name':'Monika',
    'age':25
}
```

---

Both Together

```python
def func(a, *args, **kwargs):

    print(a)
    print(args)
    print(kwargs)
```

Call

```python
func(1,2,3,x=10,y=20)
```

Output

```
1

(2,3)

{'x':10,'y':20}
```

---

CPython internally packs

```
Extra positional

↓

Tuple

Extra keyword

↓

Dictionary
```

---

# Q23. Difference between positional-only and keyword-only arguments.

Introduced in Python 3.8.

---

## Positional-only

Uses `/`

```python
def add(a, b, /):

    return a + b
```

Valid

```python
add(1,2)
```

Invalid

```python
add(a=1,b=2)
```

---

## Keyword-only

Uses `*`

```python
def connect(*, host, port):

    print(host, port)
```

Valid

```python
connect(host="localhost", port=8080)
```

Invalid

```python
connect("localhost",8080)
```

---

Combined Example

```python
def demo(a, /, b, *, c):

    print(a,b,c)
```

Call

```python
demo(1,2,c=3)
```

---

Advantages

- Better APIs
- Prevent accidental misuse
- Improve readability

---

# Q24. Explain partial functions.

A partial function fixes some arguments of another function.

Implemented using

```python
functools.partial
```

---

Example

```python
from functools import partial

def multiply(a, b):

    return a * b

double = partial(multiply, 2)

print(double(10))
```

Output

```
20
```

Memory

```
partial object

↓

Original function

↓

Stored argument = 2
```

Later

```
double(10)

↓

multiply(2,10)
```

---

Advantages

- Cleaner code
- Reusable callbacks
- Functional programming

---

# Q25. How does functools.lru_cache work?

LRU = Least Recently Used Cache

It caches function results.

---

Example

```python
from functools import lru_cache

@lru_cache(maxsize=3)
def fib(n):

    if n < 2:
        return n

    return fib(n-1) + fib(n-2)

print(fib(35))
```

Without cache

```
Exponential Time

O(2ⁿ)
```

With cache

```
Linear Time

O(n)
```

---

Internal Working

```
Function Call

↓

Arguments

↓

Hash Key

↓

Dictionary Cache

↓

Found?

Yes

↓

Return Result

No

↓

Execute Function

↓

Store Result
```

---

Eviction Policy

Suppose

```
maxsize = 3
```

Cache

```
A
B
C
```

Access

```
A
```

Cache order

```
B
C
A
```

Insert D

```
B removed

C
A
D
```

The least recently used entry is evicted.

---

Inspect Cache

```python
print(fib.cache_info())
```

Example Output

```
CacheInfo(
    hits=33,
    misses=36,
    maxsize=3,
    currsize=3
)
```

Clear Cache

```python
fib.cache_clear()
```

---

## Advantages

- Faster recursive algorithms
- API response caching
- Database query caching
- Dynamic programming
- Expensive computations

---

# Module Summary

Topics Covered

- First-class functions
- Closures
- Free variables
- Decorators vs Closures
- Function annotations
- Mutable default arguments
- *args and **kwargs
- Positional-only arguments
- Keyword-only arguments
- Partial functions
- functools.lru_cache

---

## Common Interview Follow-up Questions

1. Why do decorators usually use closures?
2. Can a closure modify an outer variable?
3. What is the purpose of the `nonlocal` keyword?
4. How are default arguments stored internally?
5. What is the difference between `functools.cache` and `lru_cache`?
6. Can `lru_cache` cache mutable arguments?
7. What is stored inside a function object's `__code__` attribute?
8. Why are functions immutable but their attributes mutable?
9. How does CPython represent free variables internally?
10. When should you use a closure instead of a class?

# Python Interview Master Guide
# Module 02A – Advanced Interview Follow-up Questions (Functions & Closures)

These are common follow-up questions asked in senior Python interviews at companies like Google, Amazon, Microsoft, Apple, Meta, and Stripe. They test your understanding of Python internals rather than just syntax.

---

# Q1. Why do decorators usually use closures?

## Short Answer

Decorators use **closures** because they need to remember the original function even after the decorator has finished executing.

A closure allows the wrapper function to retain access to the decorated function (`func`) without making it global.

---

## Example

```python
def logger(func):

    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print("Finished")
        return result

    return wrapper


@logger
def add(a, b):
    return a + b

print(add(2, 3))
```

Output

```
Calling add
Finished
5
```

---

## Memory Diagram

```
logger(add)

↓

wrapper Function
        |
        |
        +-------------------+
                            |
                            v
                      Closure Cell
                            |
                            |
                          add()
```

Even after `logger()` returns, the wrapper still remembers `func`.

---

## Why Not Use a Global Variable?

Imagine this implementation:

```python
current_function = None

def logger(func):
    global current_function
    current_function = func
```

This fails because:

- Multiple decorated functions overwrite each other.
- It is not thread-safe.
- It is not reusable.

Closures solve this elegantly.

---

## Interview Follow-up

**Q:** Are all decorators closures?

**Answer:**

Most function-based decorators use closures.

However, class-based decorators do not.

Example

```python
class Logger:

    def __init__(self, func):
        self.func = func

    def __call__(self):
        print("Calling")
        self.func()
```

---

# Q2. Can a closure modify an outer variable?

## Short Answer

Yes.

But only if the variable is declared using the `nonlocal` keyword.

---

## Without nonlocal

```python
def outer():

    count = 0

    def inner():
        count += 1

    return inner
```

Output

```
UnboundLocalError
```

Python treats `count` as a new local variable.

---

## Using nonlocal

```python
def outer():

    count = 0

    def inner():
        nonlocal count
        count += 1
        return count

    return inner

counter = outer()

print(counter())
print(counter())
print(counter())
```

Output

```
1
2
3
```

---

## Memory Diagram

```
Closure Cell

↓

count = 0

↓

count = 1

↓

count = 2

↓

count = 3
```

The closure updates the same memory location.

---

## Mutable Objects

Even without `nonlocal`, mutable objects can be modified.

```python
def outer():

    numbers = []

    def inner():
        numbers.append(1)
        return numbers

    return inner

f = outer()

print(f())
print(f())
```

Output

```
[1]
[1, 1]
```

Reason

The object isn't replaced—it is mutated.

---

# Q3. What is the purpose of the `nonlocal` keyword?

## Definition

`nonlocal` allows a nested function to modify variables from its enclosing scope.

---

## Scope Hierarchy

```
Global

↓

Outer Function

↓

Inner Function
```

Without `nonlocal`, assignments create a new local variable.

---

## Example

```python
def outer():

    x = 100

    def inner():
        nonlocal x
        x += 50
        return x

    return inner

f = outer()

print(f())
print(f())
```

Output

```
150
200
```

---

## Difference Between global and nonlocal

| Keyword | Refers To |
|----------|-----------|
| global | Global namespace |
| nonlocal | Nearest enclosing function |

Example

```python
x = 10

def outer():

    y = 20

    def inner():

        global x
        nonlocal y

        x += 1
        y += 1

    inner()
```

---

## Interview Tip

Use `nonlocal` only inside nested functions.

---

# Q4. How are default arguments stored internally?

## Key Concept

Default arguments are evaluated **once** when the function is created.

They are stored inside the function object.

---

## Example

```python
def add(a, b=10):
    return a + b
```

Inspect defaults

```python
print(add.__defaults__)
```

Output

```
(10,)
```

---

## Internal Structure

```
Function Object

|

+-----------------------+
| __defaults__          |
|-----------------------|
| (10,)                 |
+-----------------------+
```

---

## Mutable Default Problem

```python
def append_item(x, items=[]):
    items.append(x)
    return items
```

Memory

```
Function Object

↓

Defaults

↓

[]

↓

Call 1

↓

[1]

↓

Call 2

↓

[1,2]

↓

Call 3

↓

[1,2,3]
```

The same list is reused.

---

## Correct Pattern

```python
def append_item(x, items=None):

    if items is None:
        items = []

    items.append(x)

    return items
```

---

# Q5. What is the difference between `functools.cache` and `lru_cache`?

## Comparison Table

| Feature | cache | lru_cache |
|----------|--------|-----------|
| Introduced | Python 3.9 | Python 3.2 |
| Cache Size | Unlimited | Configurable |
| Eviction | No | Least Recently Used |
| maxsize | No | Yes |
| Memory Growth | Unlimited | Controlled |

---

## cache Example

```python
from functools import cache

@cache
def square(x):
    return x * x
```

Every unique input remains forever.

---

## lru_cache Example

```python
from functools import lru_cache

@lru_cache(maxsize=3)
def square(x):
    return x * x
```

Old entries are automatically removed.

---

## Internal Diagram

```
cache

Dictionary

↓

Never removes entries
```

```
lru_cache

Dictionary

+

Doubly Linked List

↓

Tracks usage order
```

---

## When to Use

Use `cache`

- Small number of unique inputs
- Unlimited memory acceptable

Use `lru_cache`

- Many possible inputs
- Memory must stay bounded

---

# Q6. Can `lru_cache` cache mutable arguments?

## Short Answer

No.

Arguments must be **hashable**.

---

## Invalid Example

```python
from functools import lru_cache

@lru_cache
def total(numbers):
    return sum(numbers)

total([1,2,3])
```

Output

```
TypeError

unhashable type: list
```

---

## Valid Example

```python
total((1,2,3))
```

Output

```
6
```

Tuple is immutable and hashable.

---

## Why?

Internally,

`lru_cache` stores results in a dictionary.

Dictionary keys must be hashable.

---

# Q7. What is stored inside a function object's `__code__` attribute?

Every Python function has a **code object**.

Inspect

```python
def add(a, b):
    return a + b

print(add.__code__)
```

---

## Useful Attributes

```python
print(add.__code__.co_varnames)
print(add.__code__.co_argcount)
print(add.__code__.co_consts)
print(add.__code__.co_names)
print(add.__code__.co_freevars)
```

---

## Common Attributes

| Attribute | Description |
|------------|-------------|
| co_argcount | Number of parameters |
| co_varnames | Local variables |
| co_consts | Constants used |
| co_names | Global names |
| co_freevars | Closure variables |
| co_cellvars | Variables shared with nested functions |
| co_code | Bytecode |

---

## Bytecode

```python
import dis

dis.dis(add)
```

Output

```
LOAD_FAST
LOAD_FAST
BINARY_ADD
RETURN_VALUE
```

---

# Q8. Why are functions immutable but their attributes mutable?

## Function Code

A function's executable code cannot be modified after creation.

However, function objects expose a writable attribute dictionary.

---

## Example

```python
def greet():
    pass

greet.author = "Monika"

greet.version = "1.0"

print(greet.author)
```

Output

```
Monika
```

---

## Memory

```
Function Object

|

+------------------+

| Code Object |

+------------------+

|

+------------------+

| __dict__ |

| author |

| version |

+------------------+
```

The code object is immutable.

The attribute dictionary is mutable.

---

## Interview Question

Can we replace a function's bytecode?

Technically yes by assigning a new `__code__` object, but it is rarely done and can easily break the program.

---

# Q9. How does CPython represent free variables internally?

Free variables are stored in **cell objects**.

---

## Example

```python
def outer():

    x = 100

    def inner():
        return x

    return inner
```

Inspect

```python
f = outer()

print(f.__closure__)
```

Output

```
(<cell at ...>,)
```

---

Access Cell

```python
print(f.__closure__[0].cell_contents)
```

Output

```
100
```

---

## Memory Diagram

```
Function Object

↓

Closure

↓

Cell Object

↓

100
```

The closure references the cell—not the raw value.

---

## CPython

Internally

```
PyCellObject

↓

PyObject *
```

Each free variable is wrapped in a cell object.

---

# Q10. When should you use a closure instead of a class?

Closures and classes both maintain state.

The choice depends on complexity.

---

## Use a Closure When

- Only one or two variables need to be remembered
- Functional programming style
- Lightweight callbacks
- Decorators
- Function factories

Example

```python
def counter():

    count = 0

    def increment():

        nonlocal count
        count += 1

        return count

    return increment

c = counter()

print(c())
print(c())
```

Output

```
1
2
```

---

## Use a Class When

- Multiple state variables
- Multiple methods
- Inheritance required
- Encapsulation needed
- Complex business logic

Example

```python
class Counter:

    def __init__(self):
        self.count = 0

    def increment(self):
        self.count += 1
        return self.count

c = Counter()

print(c.increment())
print(c.increment())
```

---

## Comparison

| Feature | Closure | Class |
|----------|----------|-------|
| State | Small | Large |
| Methods | One | Multiple |
| Inheritance | No | Yes |
| Encapsulation | Limited | Excellent |
| Readability | High for small tasks | Better for complex systems |

---

## Interview Recommendation

Use **closures** for lightweight stateful functions, decorators, callbacks, and function factories.

Use **classes** when the object models real-world entities, needs multiple behaviors, or will grow over time.

---

# Summary

This module covered advanced interview follow-up questions on:

- Decorators and closures
- Free variables
- `nonlocal`
- Function internals
- Default argument storage
- `functools.cache` vs `lru_cache`
- Function code objects
- Mutable function attributes
- CPython cell objects
- Choosing closures vs classes