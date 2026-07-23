# Python OOP Interview Guide (Module 7)

# Magic Methods (Dunder Methods)

---

# 72. What are Magic Methods?

## Definition

**Magic Methods** (also called **Dunder Methods** because they begin and end with double underscores) are **special methods automatically invoked by Python** to implement built-in behaviors for objects.

Examples:

- `__init__()`
- `__str__()`
- `__repr__()`
- `__len__()`
- `__iter__()`
- `__next__()`
- `__call__()`

These methods allow custom classes to behave like built-in Python objects.

---

## Example

```python
class Person:

    def __init__(self, name):
        self.name = name


person = Person("Alice")
```

Internally, Python automatically calls:

```python
Person.__init__(person, "Alice")
```

---

## Common Magic Methods

| Method | Purpose |
|----------|----------|
| `__init__()` | Initialize object |
| `__new__()` | Create object |
| `__str__()` | Human-readable string |
| `__repr__()` | Developer representation |
| `__len__()` | Length of object |
| `__iter__()` | Return iterator |
| `__next__()` | Return next item |
| `__call__()` | Make object callable |
| `__getitem__()` | Indexing |
| `__setitem__()` | Assignment by index |
| `__enter__()` | Context manager start |
| `__exit__()` | Context manager end |

---

## Why Use Magic Methods?

- Customize object behavior
- Integrate with Python built-ins
- Write cleaner APIs
- Support operator overloading
- Implement iteration
- Build reusable frameworks

---

## Interview Answer

> Magic methods are special methods with double underscores that Python automatically invokes to implement built-in operations such as object creation, string representation, iteration, comparison, and operator overloading.

---

# 73. `__str__()` vs `__repr__()`

Both methods return string representations of an object, but they serve different purposes.

---

## `__str__()`

- User-friendly representation
- Used by `print()`
- Intended for end users

---

## `__repr__()`

- Developer-friendly representation
- Used in the Python interpreter and debugging
- Ideally should be unambiguous and, when practical, capable of recreating the object

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


emp = Employee("Alice")

print(emp)

print(repr(emp))
```

Output

```
Employee: Alice
Employee('Alice')
```

---

## Comparison

| `__str__()` | `__repr__()` |
|--------------|--------------|
| Human-readable | Developer-oriented |
| Used by `print()` | Used by `repr()` and interactive shell |
| Friendly output | Detailed, unambiguous output |
| Optional | Optional (falls back to default if absent) |

If `__str__()` is not defined, Python falls back to `__repr__()`.

---

## Interview Answer

> `__str__()` returns a readable string for end users, while `__repr__()` returns a detailed representation intended for developers and debugging. If `__str__()` is missing, Python falls back to `__repr__()`.

---

# 74. `__call__()`

## Definition

The `__call__()` method allows an object to be **called like a function**.

---

## Example

```python
class Greeter:

    def __call__(self, name):
        return f"Hello, {name}!"


greet = Greeter()

print(greet("Alice"))
```

Output

```
Hello, Alice!
```

---

## Internally

```python
greet("Alice")
```

becomes

```python
greet.__call__("Alice")
```

---

## Real-World Uses

- Machine Learning models (`model(input)`)
- Decorators
- Dependency injection containers
- Callable service objects
- Middleware

---

## Interview Answer

> `__call__()` makes an object callable like a function. It is commonly used for callable objects, decorators, machine learning models, and dependency injection frameworks.

---

# 75. `__getattr__()`

## Definition

`__getattr__()` is called **only when an attribute is not found** using the normal lookup process.

---

## Example

```python
class User:

    def __init__(self):
        self.name = "Alice"

    def __getattr__(self, attr):
        return f"{attr} not found"


user = User()

print(user.name)

print(user.age)
```

Output

```
Alice
age not found
```

---

## Common Uses

- Default values
- Lazy loading
- Dynamic attributes
- Proxy objects

---

## Interview Answer

> `__getattr__()` is invoked only when normal attribute lookup fails. It is useful for providing default values, lazy loading, and dynamic attribute access.

---

# 76. `__getattribute__()`

## Definition

`__getattribute__()` is called **for every attribute access**, regardless of whether the attribute exists.

---

## Example

```python
class Demo:

    def __init__(self):
        self.value = 100

    def __getattribute__(self, name):
        print(f"Accessing: {name}")
        return object.__getattribute__(self, name)


d = Demo()

print(d.value)
```

Output

```
Accessing: value
100
```

---

## Important Note

Always delegate to the base implementation using:

```python
object.__getattribute__(self, name)
```

Otherwise, recursive attribute access can lead to a `RecursionError`.

---

## Difference from `__getattr__()`

| `__getattribute__()` | `__getattr__()` |
|-----------------------|-----------------|
| Called for every attribute | Called only if lookup fails |
| More powerful | Simpler fallback |
| Easier to misuse | Safer for defaults |

---

## Interview Answer

> `__getattribute__()` intercepts every attribute access and should be used carefully because it can easily cause infinite recursion. `__getattr__()` is only invoked when an attribute cannot be found.

---

# 77. `__setattr__()`

## Definition

`__setattr__()` is invoked whenever an attribute is assigned.

---

## Example

```python
class Employee:

    def __setattr__(self, key, value):
        print(f"Setting {key} = {value}")
        object.__setattr__(self, key, value)


emp = Employee()

emp.name = "Alice"
```

Output

```
Setting name = Alice
```

---

## Common Uses

- Validation
- Logging
- Audit trails
- Immutable objects

---

## Example with Validation

```python
class Employee:

    def __setattr__(self, key, value):
        if key == "salary" and value < 0:
            raise ValueError("Salary cannot be negative")

        object.__setattr__(self, key, value)
```

---

## Interview Answer

> `__setattr__()` intercepts all attribute assignments and is commonly used for validation, logging, and enforcing business rules. It should delegate to `object.__setattr__()` to avoid infinite recursion.

---

# 78. `__delattr__()`

## Definition

`__delattr__()` is called whenever an attribute is deleted using `del`.

---

## Example

```python
class Employee:

    def __init__(self):
        self.name = "Alice"

    def __delattr__(self, item):
        print(f"Deleting {item}")
        object.__delattr__(self, item)


emp = Employee()

del emp.name
```

Output

```
Deleting name
```

---

## Use Cases

- Logging
- Audit trails
- Restricting deletion of critical attributes

---

## Example

```python
class Employee:

    def __delattr__(self, item):
        if item == "salary":
            raise AttributeError("Cannot delete salary")

        object.__delattr__(self, item)
```

---

## Interview Answer

> `__delattr__()` is triggered when an attribute is deleted. It can be used to log deletions, enforce business rules, or prevent deletion of important attributes.

---

# 79. `__len__()`

## Definition

`__len__()` defines the behavior of the built-in `len()` function for custom objects.

---

## Example

```python
class Team:

    def __init__(self):
        self.members = ["Alice", "Bob", "Charlie"]

    def __len__(self):
        return len(self.members)


team = Team()

print(len(team))
```

Output

```
3
```

---

## Internally

```python
len(team)
```

becomes

```python
team.__len__()
```

---

## Use Cases

- Collections
- Containers
- Custom data structures

---

## Interview Answer

> `__len__()` allows custom objects to define their length and integrate with Python's built-in `len()` function.

---

# 80. `__iter__()` and `__next__()`

These methods implement the **Iterator Protocol**.

---

## `__iter__()`

Returns an iterator object.

---

## `__next__()`

Returns the next item from the iterator.

When there are no more items, it must raise:

```python
StopIteration
```

---

## Example

```python
class Counter:

    def __init__(self, limit):
        self.current = 1
        self.limit = limit

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > self.limit:
            raise StopIteration

        value = self.current
        self.current += 1
        return value


counter = Counter(3)

for number in counter:
    print(number)
```

Output

```
1
2
3
```

---

## Internally

```python
for item in counter:
    print(item)
```

is roughly equivalent to:

```python
iterator = iter(counter)

while True:
    try:
        item = next(iterator)
        print(item)
    except StopIteration:
        break
```

---

## Difference

| `__iter__()` | `__next__()` |
|---------------|--------------|
| Returns an iterator | Returns the next value |
| Called once at iteration start | Called repeatedly |
| Must return an iterator object | Must raise `StopIteration` when exhausted |

---

## Common Uses

- File objects
- Database cursors
- Generators
- Streaming APIs
- Custom collections

---

## Interview Answer

> `__iter__()` returns an iterator object, while `__next__()` returns the next element during iteration. Together they implement Python's iterator protocol and enable objects to work with `for` loops and functions like `iter()` and `next()`.

---

# Module Summary

- Magic methods (dunder methods) allow custom classes to integrate seamlessly with Python's built-in features.
- `__str__()` provides a user-friendly string representation, while `__repr__()` provides a developer-oriented representation.
- `__call__()` makes an object callable like a function.
- `__getattr__()` handles missing attributes, whereas `__getattribute__()` intercepts every attribute access.
- `__setattr__()` intercepts attribute assignments, and `__delattr__()` intercepts attribute deletions.
- `__len__()` enables custom objects to work with `len()`.
- `__iter__()` and `__next__()` implement the iterator protocol, allowing objects to be used in `for` loops.
- Magic methods are widely used in Python frameworks such as Django, FastAPI, SQLAlchemy, NumPy, and pandas to provide intuitive and Pythonic APIs.