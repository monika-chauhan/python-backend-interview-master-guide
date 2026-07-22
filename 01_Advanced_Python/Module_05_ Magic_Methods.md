# Module 5: Magic Methods (Questions 51–60)

# Q51. Explain `__call__`.

## Answer

The `__call__` magic method allows an object to behave like a function.

Instead of writing:

```python
obj.method()
```

you can directly call:

```python
obj()
```

When Python sees `obj()`, it internally executes:

```python
obj.__call__()
```

---

## Syntax

```python
class MyClass:
    def __call__(self):
        print("Object called")
```

Usage:

```python
obj = MyClass()

obj()
```

Output

```
Object called
```

---

## Example 1 — Function-like Object

```python
class Adder:

    def __init__(self, value):
        self.value = value

    def __call__(self, x):
        return self.value + x

add10 = Adder(10)

print(add10(5))
```

Output

```
15
```

The object behaves exactly like a function.

---

## Example 2 — Machine Learning Style

Many ML libraries use `__call__`.

```python
class Model:

    def __call__(self, data):
        print("Running prediction")

model = Model()

model("image")
```

Instead of

```python
model.predict(image)
```

they simply use

```python
model(image)
```

---

## Real-world Uses

- PyTorch models
- TensorFlow layers
- Decorators
- Dependency Injection
- Callable Validators
- Middleware

---

## Interview Follow-up

**Q:** Can an object have both methods and `__call__`?

Yes.

```python
class Demo:

    def hello(self):
        print("Hello")

    def __call__(self):
        print("Called")

d = Demo()

d.hello()

d()
```

Output

```
Hello
Called
```

---

## Time Complexity

O(1)

---

## Senior Interview Tip

`__call__` is commonly used when objects need to maintain internal state while behaving like functions.

---

# Q52. Difference between `__str__` and `__repr__`

## Answer

Both methods define the string representation of an object.

However, they serve different purposes.

| Feature | `__str__` | `__repr__` |
|----------|-----------|------------|
| Audience | End User | Developer |
| Goal | Readable | Unambiguous |
| Used by | `print()` | REPL, `repr()` |
| Should recreate object? | No | Ideally Yes |

---

## Example

```python
class Employee:

    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f"Employee: {self.name}"

    def __repr__(self):
        return f"Employee('{self.name}')"

e = Employee("Alice")

print(str(e))
print(repr(e))
```

Output

```
Employee: Alice

Employee('Alice')
```

---

## If Only `__repr__` Exists

```python
class Demo:

    def __repr__(self):
        return "Demo Object"

d = Demo()

print(d)
```

Output

```
Demo Object
```

Python automatically falls back to `__repr__`.

---

## Default Representation

Without either method:

```
<__main__.Demo object at 0x10783d940>
```

---

## Best Practice

Always implement `__repr__`.

Implement `__str__` only if users need a cleaner display.

---

## Interview Question

**Why is `__repr__` important?**

Because debugging becomes significantly easier.

---

# Q53. Implement Custom Iterator

## Answer

An iterator is an object that implements

- `__iter__()`
- `__next__()`

---

## Example

```python
class Counter:

    def __init__(self, limit):
        self.limit = limit
        self.current = 1

    def __iter__(self):
        return self

    def __next__(self):

        if self.current > self.limit:
            raise StopIteration

        value = self.current
        self.current += 1
        return value

counter = Counter(5)

for i in counter:
    print(i)
```

Output

```
1
2
3
4
5
```

---

## How it Works

Python internally performs:

```python
iterator = iter(counter)

while True:

    try:
        value = next(iterator)
        print(value)

    except StopIteration:
        break
```

---

## Real-world Uses

- Reading huge files
- Pagination
- Database cursors
- Streaming APIs

---

## Complexity

Memory: O(1)

---

# Q54. Iterator vs Iterable

## Answer

Many developers confuse these concepts.

---

## Iterable

An iterable is an object that can produce an iterator.

Examples:

```python
list
tuple
set
dict
string
```

---

## Iterator

An iterator produces one item at a time.

It implements

```python
__iter__()
__next__()
```

---

## Example

```python
nums = [1,2,3]

iterator = iter(nums)

print(next(iterator))
print(next(iterator))
print(next(iterator))
```

Output

```
1
2
3
```

---

## Comparison

| Iterable | Iterator |
|-----------|----------|
| Can create iterator | Produces values |
| Uses `iter()` | Uses `next()` |
| Doesn't track state | Tracks current position |
| Examples: list, tuple | Created by `iter()` |

---

## Interview Question

**Is every iterator iterable?**

Yes.

Because iterator implements

```python
__iter__()
```

returning itself.

---

# Q55. Generator Internals

## Answer

A generator is a special iterator automatically created using `yield`.

Instead of storing all values in memory, it pauses execution after each `yield`.

---

## Example

```python
def numbers():

    yield 1
    yield 2
    yield 3

g = numbers()

print(next(g))
print(next(g))
```

Output

```
1
2
```

---

## Internal State

The generator remembers

- local variables
- current line
- execution stack

Execution resumes exactly where it stopped.

---

## Equivalent Iterator

Without generator:

```python
class Numbers:

    def __iter__(self):
        return self

    def __next__(self):
        ...
```

Generators automatically implement this.

---

## Advantages

- Lazy evaluation
- Memory efficient
- Faster for large datasets
- Streaming

---

## Complexity

Memory

O(1)

---

# Q56. Yield vs Return

## Answer

| Yield | Return |
|--------|---------|
| Pauses function | Ends function |
| Creates generator | Returns value |
| Can execute multiple times | Executes once |
| Lazy | Eager |

---

## Return Example

```python
def demo():
    return 10
    return 20

print(demo())
```

Output

```
10
```

Function exits immediately.

---

## Yield Example

```python
def demo():

    yield 10
    yield 20

g = demo()

print(next(g))
print(next(g))
```

Output

```
10
20
```

---

## Memory Comparison

Return

```
Creates entire object.
```

Yield

```
Creates one object at a time.
```

---

## Best Use Cases

Return

- APIs
- Mathematical computation

Yield

- File processing
- Infinite sequences
- Streaming
- Large datasets

---

# Q57. Explain `yield from`

## Answer

`yield from` delegates iteration to another iterable or generator.

Instead of writing:

```python
for item in generator:
    yield item
```

we simply write

```python
yield from generator
```

---

## Example

```python
def first():
    yield 1
    yield 2

def second():
    yield from first()
    yield 3

for i in second():
    print(i)
```

Output

```
1
2
3
```

---

## Equivalent Code

```python
def second():

    for item in first():
        yield item

    yield 3
```

---

## Benefits

- Cleaner
- Faster
- Easier to read
- Supports nested generators

---

# Q58. Context Managers (`__enter__`, `__exit__`)

## Answer

Context managers automatically acquire and release resources.

Most commonly used with

```python
with
```

---

## Example

```python
with open("file.txt") as f:
    print(f.read())
```

Python internally executes:

```python
obj = open(...)

obj.__enter__()

try:
    ...

finally:
    obj.__exit__()
```

---

## Methods

```python
__enter__()

__exit__(exc_type, exc_value, traceback)
```

---

## Benefits

- No resource leaks
- Automatic cleanup
- Exception handling
- Cleaner code

---

## Real-world Examples

- Files
- Database connections
- Locks
- Network sockets

---

# Q59. Implement Your Own Context Manager

## Answer

```python
class Database:

    def __enter__(self):
        print("Connection opened")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Connection closed")

with Database():
    print("Executing query")
```

Output

```
Connection opened
Executing query
Connection closed
```

---

## Exception Handling

```python
class Demo:

    def __enter__(self):
        print("Start")

    def __exit__(self, exc_type, exc_val, exc_tb):
        print("Cleanup")
        return True

with Demo():
    10 / 0
```

Output

```
Start
Cleanup
```

Returning `True` suppresses the exception.

---

## Best Practice

Always release resources inside `__exit__`, even if an exception occurs.

---

# Q60. Async Context Manager

## Answer

Async context managers are used with asynchronous code and the `async with` statement.

They implement:

- `__aenter__()`
- `__aexit__()`

---

## Example

```python
import asyncio

class AsyncConnection:

    async def __aenter__(self):
        print("Opening connection")
        return self

    async def __aexit__(self, exc_type, exc_val, exc_tb):
        print("Closing connection")

async def main():

    async with AsyncConnection():
        print("Working...")

asyncio.run(main())
```

Output

```
Opening connection
Working...
Closing connection
```

---

## Real-world Uses

- Async database connections
- HTTP clients (`aiohttp`)
- Async file operations
- Async locks
- Message queues

---

## Difference from Regular Context Manager

| Regular | Async |
|----------|-------|
| `with` | `async with` |
| `__enter__` | `__aenter__` |
| `__exit__` | `__aexit__` |
| Synchronous cleanup | Asynchronous cleanup |

---

## Senior Interview Tip

Use async context managers when resource acquisition or cleanup involves `await`, such as opening an async database connection or HTTP session. They prevent blocking the event loop and ensure resources are released even if exceptions occur.

---
**End of Module 5 (Questions 51–60)**