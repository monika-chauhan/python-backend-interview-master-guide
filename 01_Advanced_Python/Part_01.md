# Python Interview Master Guide
# Module 01 - Python Internals (Questions 1-15)

---

# Q1. How are Python objects represented in memory?

Every value in Python is an object.

Internally, CPython represents every object using the `PyObject` structure.

```c
typedef struct _object {
    Py_ssize_t ob_refcnt;
    PyTypeObject *ob_type;
} PyObject;
```

Every Python object contains two mandatory fields:

| Field | Purpose |
|--------|----------|
| ob_refcnt | Reference count |
| ob_type | Pointer to object's type |

Example:

```python
x = 10
```

Memory

```
Stack

x
 |
 |
 v

+----------------------+
| PyLongObject         |
|----------------------|
| ref_count = 1        |
| type = int           |
| value = 10           |
+----------------------+
```

For strings

```python
name = "Monika"
```

```
Stack

name
  |
  v

+----------------------+
| PyUnicodeObject      |
|----------------------|
| ref_count            |
| type                 |
| unicode data         |
+----------------------+
```

Every object additionally stores object-specific data after the PyObject header.

Example

```python
lst = [1,2,3]
```

Memory

```
PyListObject

+-------------------+
| ref_count         |
| type              |
| size              |
| allocated         |
| pointer ----------+
+-------------------+
                    |
                    v
            +-------------+
            | PyLong(1)   |
            | PyLong(2)   |
            | PyLong(3)   |
            +-------------+
```

Interview Follow-up

- Why are Python objects larger than C objects?
- Why is int slower than primitive int in Java?

Answer:
Because every object stores metadata.

---

# Q2. Explain the CPython memory model.

CPython memory is divided into multiple layers.

```
OS Memory

    |

Virtual Memory

    |

CPython Allocator

    |

PyMalloc

    |

Objects
```

Memory allocation happens in three levels.

## Level 1

Operating System

Uses malloc/free.

---

## Level 2

PyMalloc

Optimized allocator for small objects (<512 bytes)

Advantages

- Faster
- Reduces fragmentation
- Reuses memory pools

---

## Level 3

Python Objects

Objects are created from arenas.

```
Arena (256 KB)

|
|-- Pool (4 KB)
      |
      |-- Blocks
```

Memory hierarchy

```
Arena

    |
    +--- Pool
             |
             +--- Block
```

Interview Question

Why doesn't Python immediately return memory to OS?

Answer

Memory is usually returned to Python's allocator, not the operating system.

---

# Q3. What is reference counting? How does Python's garbage collector complement it?

Every object maintains a reference count.

Example

```python
a = []

b = a

c = a
```

```
Object []

ref_count = 3
```

Deleting references

```python
del b
```

```
ref_count = 2
```

When reference count becomes zero

```
Object destroyed immediately.
```

Example

```python
def func():
    x = [1,2,3]

func()
```

After function exits

```
ref_count = 0

Memory freed
```

Problem

Reference counting cannot detect cycles.

```
A ---> B
^      |
|______|
```

Both objects always have reference count >0.

Garbage Collector solves this.

Interview Follow-up

Why does CPython use reference counting?

Answer

Immediate destruction of objects.

---

# Q4. Explain Python's cyclic garbage collection algorithm.

Python uses Generational Garbage Collection.

Three generations

```
Generation 0

Generation 1

Generation 2
```

New objects

↓

Generation 0

If object survives

↓

Generation 1

Survives again

↓

Generation 2

Cycle Example

```python
class Node:

    pass

a = Node()

b = Node()

a.next = b

b.next = a
```

```
A ---> B

^      |

|______|
```

Reference count never becomes zero.

GC algorithm

1. Detect unreachable cycles
2. Break cycle
3. Free memory

You can manually trigger

```python
import gc

gc.collect()
```

View statistics

```python
import gc

print(gc.get_count())
```

Disable GC

```python
gc.disable()
```

---

# Q5. What are memory leaks in Python? How do you detect and fix them?

Python can leak memory.

Reasons

- Global caches
- Circular references
- Unclosed files
- Large global objects
- Closures

Example

```python
cache = []

while True:

    cache.append(bytearray(1000000))
```

Memory continuously increases.

Detection

```python
import tracemalloc

tracemalloc.start()
```

Snapshot

```python
snapshot = tracemalloc.take_snapshot()

print(snapshot.statistics('lineno'))
```

Useful tools

- tracemalloc
- gc
- objgraph
- memory_profiler

Fixes

- Weak references
- Close files
- Remove global references
- Use generators

---

# Q6. What is the difference between id(), is, and ==?

Example

```python
a = [1]

b = [1]
```

```
id(a) != id(b)
```

==

Compares values.

```python
a == b

True
```

is

Compares memory address.

```python
a is b

False
```

id()

Returns memory identity.

```python
id(a)
```

Interview Rule

Use

```
is None
```

instead of

```
== None
```

---

# Q7. Explain Python object interning.

Interning means

Reuse immutable objects.

Example

```python
a = "hello"

b = "hello"
```

```
Both point to same object.
```

```
a is b

True
```

Python interns

- identifiers
- small strings
- compile-time constants

Manual interning

```python
import sys

a = sys.intern("python")
```

---

# Q8. Why are small integers cached?

CPython caches

```
-5

to

256
```

Example

```python
a = 100

b = 100

a is b
```

Output

```
True
```

But

```python
a = 500

b = 500
```

May return

```
False
```

Reason

Performance.

Millions of integer allocations avoided.

---

# Q9. How does Python manage namespaces?

Namespace

Dictionary mapping

```
Name

↓

Object
```

Types

- Built-in
- Global
- Local
- Enclosing

Example

```python
x = 100

def fun():

    y = 50
```

Namespaces

```
Global

x

↓

100

Local

y

↓

50
```

---

# Q10. Explain LEGB lookup with implementation details.

LEGB

```
Local

↓

Enclosing

↓

Global

↓

Built-in
```

Example

```python
x = 100

def outer():

    x = 50

    def inner():

        print(x)

    inner()
```

Output

```
50
```

Lookup order

```
inner()

↓

outer()

↓

global

↓

builtins
```

---

# Q11. How are local variables optimized in CPython?

CPython stores local variables in an array instead of dictionary.

Reason

Array lookup

```
O(1)
```

Dictionary lookup

Slower.

Inspect bytecode

```python
import dis

def add():

    x = 10

    return x

dis.dis(add)
```

Output

```
LOAD_CONST

STORE_FAST

LOAD_FAST
```

STORE_FAST

Shows optimized local storage.

---

# Q12. Explain Python bytecode generation.

Flow

```
Source

↓

Parser

↓

AST

↓

Compiler

↓

Bytecode

↓

Python VM
```

Example

```python
x = 10 + 20
```

Inspect

```python
import dis

dis.dis("x=10+20")
```

VM executes bytecode.

---

# Q13. What is the purpose of .pyc files?

Compiled bytecode cache.

Location

```
__pycache__
```

Example

```
test.py

↓

__pycache__

test.cpython-312.pyc
```

Benefits

- Faster imports
- No recompilation

Not machine code.

Only Python bytecode.

---

# Q14. How does Python import work internally?

Import Steps

```
import math

↓

Check sys.modules

↓

Already loaded?

↓

Return

↓

Else

↓

Find module

↓

Compile

↓

Execute

↓

Store in sys.modules
```

Import happens only once.

---

# Q15. Explain sys.modules and module caching.

Every imported module is cached.

```python
import sys

print(sys.modules.keys())
```

```
dict

{

"os":...

"math":...

}
```

Example

```python
import math

import math

print(id(math))
```

Both imports return same object.

Benefits

- Faster imports
- Singleton modules
- Prevent repeated execution

Interview Question

Difference between

```
reload(module)
```

and

```
import module
```

Answer

reload()

Re-executes module code.

import

Uses cached module.

---

# End of Module 01

Topics Covered

- Python Object Model
- Memory Management
- Reference Counting
- Garbage Collection
- Object Interning
- Integer Caching
- Namespaces
- LEGB
- Bytecode
- Import System
- sys.modules
