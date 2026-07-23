# Python OOP Interview Guide (Module 8)

# Python Data Model

---

# 81. What is the Python Data Model?

## Definition

The **Python Data Model** defines how Python objects behave and interact with the language.

It specifies a set of **special (magic/dunder) methods** that allow custom objects to integrate seamlessly with Python's built-in features.

Examples include:

- Object creation
- Attribute access
- String representation
- Arithmetic operations
- Comparison
- Iteration
- Context management
- Hashing

When you use Python syntax like `+`, `len()`, `for`, or `print()`, Python internally calls these special methods.

---

## Example

```python
class Book:

    def __init__(self, title):
        self.title = title

    def __str__(self):
        return self.title


book = Book("Python")

print(book)
```

Internally:

```python
print(book)
```

becomes

```python
print(book.__str__())
```

---

## Diagram

```
Python Syntax

      |

      ▼

Magic Methods

      |

      ▼

Custom Object Behavior
```

---

## Why is it Important?

The data model enables custom objects to behave like built-in types such as:

- list
- dict
- set
- tuple
- str

---

## Interview Answer

> The Python Data Model is a collection of protocols implemented through special (dunder) methods. It defines how objects behave with Python's syntax, allowing custom classes to integrate with built-in operations such as iteration, arithmetic, comparison, hashing, and context management.

---

# 82. Why are Magic Methods Important?

Magic methods make Python objects **Pythonic**.

Without them, custom objects cannot interact naturally with Python's built-in functions and operators.

---

## Example

Without `__len__()`

```python
class Team:
    pass


team = Team()

len(team)
```

Output

```
TypeError
```

---

With `__len__()`

```python
class Team:

    def __len__(self):
        return 10


team = Team()

print(len(team))
```

Output

```
10
```

---

## Common Magic Methods

| Method | Enables |
|----------|----------|
| `__str__()` | `print(obj)` |
| `__repr__()` | `repr(obj)` |
| `__len__()` | `len(obj)` |
| `__iter__()` | `for` loops |
| `__next__()` | `next()` |
| `__call__()` | Function-call syntax |
| `__getitem__()` | Indexing |
| `__contains__()` | `in` operator |
| `__eq__()` | Equality (`==`) |
| `__hash__()` | Hash-based collections |

---

## Interview Answer

> Magic methods allow custom objects to integrate with Python's language features and built-in functions. They enable behaviors such as iteration, printing, comparison, operator overloading, hashing, and context management.

---

# 83. How Does Python Know an Object is Iterable?

Python determines whether an object is iterable using the **Iterator Protocol**.

---

## Rule 1

The object should implement:

```python
__iter__()
```

which returns an iterator.

---

## Rule 2

The iterator must implement:

```python
__next__()
```

which returns the next item or raises `StopIteration`.

---

## Example

```python
class Counter:

    def __init__(self):
        self.current = 1

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > 3:
            raise StopIteration

        value = self.current
        self.current += 1
        return value


for num in Counter():
    print(num)
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
for item in obj:
```

is roughly translated to:

```python
iterator = iter(obj)

while True:
    try:
        item = next(iterator)
    except StopIteration:
        break
```

---

## Interview Answer

> Python considers an object iterable if it implements the iterator protocol. Typically, `__iter__()` returns an iterator, and that iterator implements `__next__()` to provide successive values until `StopIteration` is raised.

---

# 84. Context Manager and `__enter__()` / `__exit__()`

## What is a Context Manager?

A context manager manages resources by automatically performing setup and cleanup operations.

It is commonly used with the `with` statement.

---

## Example

```python
with open("data.txt") as file:
    print(file.read())
```

Python automatically closes the file.

---

## Custom Context Manager

```python
class Database:

    def __enter__(self):
        print("Connection Opened")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        print("Connection Closed")


with Database():
    print("Working...")
```

Output

```
Connection Opened
Working...
Connection Closed
```

---

## Execution Flow

```
with Database()

↓

__enter__()

↓

Code Block

↓

__exit__()
```

---

## Benefits

- Automatic cleanup
- Resource safety
- Exception handling
- Cleaner code

---

## Interview Answer

> A context manager controls resource acquisition and release. The `__enter__()` method is called at the beginning of a `with` block, and `__exit__()` is called at the end, even if an exception occurs.

---

# 85. Callable Objects

## Definition

A callable object is an object that can be invoked like a function.

This is achieved by implementing:

```python
__call__()
```

---

## Example

```python
class Multiplier:

    def __init__(self, factor):
        self.factor = factor

    def __call__(self, value):
        return value * self.factor


double = Multiplier(2)

print(double(10))
```

Output

```
20
```

---

## Common Uses

- Machine learning models
- Decorators
- Middleware
- Validators
- Dependency injection containers

---

## Interview Answer

> A callable object implements the `__call__()` method, allowing instances of the class to be invoked like functions while maintaining internal state.

---

# 86. Hashable Objects

## Definition

An object is **hashable** if:

1. It has a hash value.
2. The hash value remains unchanged during its lifetime.
3. It can be compared using `==`.

Hashable objects can be used as:

- Dictionary keys
- Set elements

---

## Hashable Types

```python
int

float

str

tuple (if all elements are hashable)

frozenset
```

---

## Non-Hashable Types

```python
list

dict

set
```

These are mutable.

---

## Example

```python
print(hash("Python"))

print(hash((1, 2)))
```

---

## Interview Answer

> A hashable object has a stable hash value and supports equality comparison. Hashable objects can be used as dictionary keys and set elements because their hash does not change over their lifetime.

---

# 87. Immutable Objects and Hashing

Immutable objects are generally hashable because their values cannot change.

---

## Immutable Types

- int
- float
- str
- tuple (if contents are hashable)
- bytes
- frozenset

---

## Mutable Types

- list
- dict
- set

---

## Example

```python
hash("Python")

hash((1, 2))
```

Valid.

---

```python
hash([1, 2])
```

Output

```
TypeError
```

---

## Why?

If mutable objects were hashable, changing their value after insertion into a dictionary could corrupt the hash table.

---

## Interview Answer

> Immutable objects are usually hashable because their state cannot change, ensuring that their hash value remains stable throughout their lifetime.

---

# 88. Equality vs Hash

These concepts are related but different.

---

## Equality

Uses:

```python
__eq__()
```

Determines whether two objects are logically equal.

---

## Hash

Uses:

```python
__hash__()
```

Determines the bucket location in hash-based collections.

---

## Important Rule

If:

```python
a == b
```

Then:

```python
hash(a) == hash(b)
```

must also be true.

The reverse is **not** guaranteed.

Different objects may share the same hash value (a hash collision).

---

## Example

```python
class User:

    def __init__(self, id):
        self.id = id

    def __eq__(self, other):
        return self.id == other.id

    def __hash__(self):
        return hash(self.id)
```

---

## Interview Answer

> Equality determines whether two objects represent the same value, while hashing determines where an object is stored in hash-based collections. Equal objects must produce the same hash value.

---

# 89. Custom Hashing

Custom classes can define their own hashing logic.

---

## Example

```python
class Employee:

    def __init__(self, emp_id):
        self.emp_id = emp_id

    def __eq__(self, other):
        return self.emp_id == other.emp_id

    def __hash__(self):
        return hash(self.emp_id)


e1 = Employee(101)

e2 = Employee(101)

print(hash(e1))

print(e1 == e2)
```

Output

```
True
```

Both objects compare equal and produce the same hash.

---

## Best Practice

Use only **immutable attributes** in `__hash__()`.

Changing an attribute used in hashing after insertion into a dictionary or set can break lookups.

---

## Interview Answer

> Custom hashing is implemented by overriding `__hash__()`. The hash should be derived from immutable fields, and any objects considered equal by `__eq__()` must return the same hash value.

---

# 90. Dictionary Internals with Objects

Python dictionaries are implemented using **hash tables**.

---

## Lookup Process

Suppose:

```python
employees = {
    employee: "Developer"
}
```

When accessing:

```python
employees[employee]
```

Python performs:

### Step 1

Calls

```python
employee.__hash__()
```

to compute the hash.

---

### Step 2

Uses the hash to locate the appropriate bucket.

---

### Step 3

If multiple objects share the same hash (collision), Python compares keys using:

```python
employee.__eq__()
```

to identify the correct object.

---

## Diagram

```
Object

 │

 ▼

__hash__()

 │

 ▼

Hash Table Bucket

 │

 ▼

Collision?

 │

 ▼

__eq__()

 │

 ▼

Correct Value
```

---

## Example

```python
class User:

    def __init__(self, user_id):
        self.user_id = user_id

    def __hash__(self):
        return hash(self.user_id)

    def __eq__(self, other):
        return self.user_id == other.user_id


u1 = User(1)

u2 = User(1)

data = {
    u1: "Alice"
}

print(data[u2])
```

Output

```
Alice
```

Although `u1` and `u2` are different instances, they are treated as the same key because they have equal hashes and compare equal.

---

## Time Complexity

Average-case operations:

- Lookup → **O(1)**
- Insert → **O(1)**
- Delete → **O(1)**

Worst case (many collisions):

- **O(n)**

Python minimizes collisions through a high-quality hash function and efficient hash table implementation.

---

## Interview Answer

> Dictionaries use hash tables internally. Python first computes the key's hash using `__hash__()` to locate a bucket. If multiple keys share the same hash, it resolves collisions by comparing keys with `__eq__()`. This provides average-case O(1) lookup, insertion, and deletion.

---

# Module Summary

- The Python Data Model defines how objects interact with the language through special (dunder) methods.
- Magic methods enable custom objects to behave like built-in types.
- Objects are iterable if they implement the iterator protocol using `__iter__()` and `__next__()`.
- Context managers use `__enter__()` and `__exit__()` to manage resources safely.
- Callable objects implement `__call__()`.
- Hashable objects have a stable hash value and can be used as dictionary keys and set elements.
- Immutable objects are generally hashable because their state does not change.
- Equality (`__eq__()`) determines logical equivalence, while hashing (`__hash__()`) determines placement in hash-based collections.
- When overriding `__eq__()`, ensure that equal objects produce the same hash.
- Python dictionaries use hash tables, relying on `__hash__()` for bucket selection and `__eq__()` for collision resolution, providing average-case O(1) performance.