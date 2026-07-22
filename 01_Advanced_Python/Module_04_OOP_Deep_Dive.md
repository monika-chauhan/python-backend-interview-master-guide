# Python Interview Master Guide
# Module 04 – OOP Deep Dive (Questions 36–50)

---

# Introduction

Python's Object-Oriented Programming (OOP) model is significantly different from languages like Java and C++.

Some key characteristics are:

- Everything is an object.
- Classes themselves are objects.
- Object creation is highly customizable.
- Dynamic dispatch.
- Multiple inheritance.
- Metaclass-based class creation.
- Powerful descriptor protocol.

This module covers Python OOP from both **language** and **CPython implementation** perspectives.

---

# Q36. Explain Python's object model.

## Definition

In Python,

> **Everything is an object.**

Even these are objects:

- integers
- strings
- lists
- functions
- classes
- modules

Example

```python
print(type(10))
print(type("hello"))
print(type(list))
print(type(int))
```

Output

```
<class 'int'>
<class 'str'>
<class 'type'>
<class 'type'>
```

Notice that even `int` and `list` are objects of type `type`.

---

## Memory Representation

```
10

↓

PyLongObject

↓

PyObject

↓

Reference Count

↓

Type Pointer
```

Every object contains

```
PyObject

↓

Reference Count

↓

Type Pointer
```

---

## Object Identity

Every object has

- Identity
- Type
- Value

Example

```python
x = 10

print(id(x))
print(type(x))
```

---

## Everything Inherits from object

```python
class A:
    pass

print(isinstance(A(), object))
```

Output

```
True
```

---

## Interview Tip

Python does not have primitive types.

Everything is an object.

---

# Q37. How does __new__ differ from __init__?

Python object creation occurs in two phases.

```
__new__()

↓

creates object

↓

__init__()

↓

initializes object
```

---

## __new__

Responsible for allocating memory.

Must return an object.

```python
class Person:

    def __new__(cls):

        print("Creating object")

        return super().__new__(cls)

    def __init__(self):

        print("Initializing")
```

Output

```
Creating object

Initializing
```

---

## __init__

Initializes an already-created object.

Cannot return another object.

---

## Memory Flow

```
Person()

↓

__new__()

↓

Memory Allocated

↓

Object Returned

↓

__init__()

↓

Fields Initialized
```

---

## Common Uses of __new__

- Singleton
- Immutable objects
- Object caching
- Custom allocation

---

## Interview Question

Can `__init__` return an object?

No.

Returning anything except `None` raises

```
TypeError
```

---

# Q38. Explain Method Resolution Order (MRO).

MRO determines the order in which Python searches for methods.

Example

```python
class A:

    def show(self):
        print("A")


class B(A):

    pass


class C(B):

    pass

C().show()
```

Output

```
A
```

Search order

```
C

↓

B

↓

A

↓

object
```

---

## View MRO

```python
print(C.__mro__)
```

or

```python
print(C.mro())
```

---

## Why MRO?

Without it,

Python wouldn't know which parent method to call in multiple inheritance.

---

# Q39. Diamond inheritance problem.

Example

```python
class A:

    def show(self):
        print("A")


class B(A):

    pass


class C(A):

    pass


class D(B, C):

    pass
```

Hierarchy

```
      A
     / \
    B   C
     \ /
      D
```

Question

Which `show()` should D use?

Without MRO,

```
Ambiguous
```

---

## Python Solution

Uses

```
C3 Linearization
```

MRO

```
D

↓

B

↓

C

↓

A

↓

object
```

---

# Q40. Explain C3 linearization.

Python computes MRO using the

```
C3 Linearization Algorithm
```

Goals

- Preserve inheritance order
- Preserve local precedence
- No duplicates
- Consistent ordering

---

Example

```python
class A: pass
class B(A): pass
class C(A): pass
class D(B,C): pass
```

Result

```
D

↓

B

↓

C

↓

A

↓

object
```

Inspect

```python
print(D.mro())
```

---

## Why C3?

Older algorithms produced inconsistent lookup paths.

C3 guarantees deterministic behavior.

---

# Q41. Difference between staticmethod, classmethod, and instance method.

## Instance Method

Receives

```
self
```

```python
class User:

    def greet(self):
        print(self)
```

---

## Class Method

Receives

```
cls
```

```python
class User:

    @classmethod
    def info(cls):
        print(cls)
```

Useful for

- Factory methods
- Alternative constructors

---

## Static Method

Receives neither

```
self

nor

cls
```

```python
class Math:

    @staticmethod
    def add(a,b):
        return a+b
```

---

## Comparison

| Type | Receives |
|-------|----------|
| Instance | self |
| Class | cls |
| Static | Nothing |

---

# Q42. Explain descriptors.

Descriptors are objects implementing

```
__get__

__set__

__delete__
```

They customize attribute access.

---

Example

```python
class Descriptor:

    def __get__(self, obj, owner):
        return "Reading"

    def __set__(self, obj, value):
        print("Writing", value)


class Test:

    value = Descriptor()


t = Test()

print(t.value)

t.value = 100
```

Output

```
Reading

Writing 100
```

---

## Built-in Descriptors

- property
- staticmethod
- classmethod
- functions

---

# Q43. What are data and non-data descriptors?

## Data Descriptor

Implements

```
__get__

and

__set__
```

Example

```python
class Data:

    def __get__(self,obj,objtype):
        ...

    def __set__(self,obj,value):
        ...
```

---

## Non-data Descriptor

Implements only

```
__get__
```

Example

Functions

```
def hello():
    ...
```

are non-data descriptors.

---

## Priority

```
Data Descriptor

↓

Instance Dictionary

↓

Non-data Descriptor

↓

Class Dictionary
```

Data descriptors always win.

---

# Q44. Implement property manually.

Python's built-in property uses descriptors.

Manual implementation

```python
class Property:

    def __init__(self,getter):

        self.getter = getter

    def __get__(self,obj,objtype):

        return self.getter(obj)


class User:

    def __init__(self):

        self._name="Monika"

    @Property

    def name(self):

        return self._name


u=User()

print(u.name)
```

Output

```
Monika
```

---

# Q45. Explain __slots__.

Normally

```
object

↓

__dict__
```

stores attributes.

Example

```python
class User:

    pass
```

Each object owns

```
__dict__
```

---

Using

```python
class User:

    __slots__=("name","age")
```

Memory

```
Object

↓

Fixed Slots

↓

name

age
```

No dictionary created.

---

## Advantages

- Lower memory
- Faster attribute access

---

## Limitations

- Cannot add arbitrary attributes
- Less flexible

---

# Q46. What are metaclasses?

Metaclass

```
creates classes
```

Just as

```
class

creates objects
```

```
Object

↓

Class

↓

Metaclass
```

---

Everything ultimately uses

```
type
```

```python
class User:
    pass

print(type(User))
```

Output

```
<class 'type'>
```

---

Custom Metaclass

```python
class Meta(type):

    def __new__(cls,name,bases,attrs):

        print("Creating",name)

        return super().__new__(cls,name,bases,attrs)


class User(metaclass=Meta):

    pass
```

---

# Q47. When should metaclasses be used?

Use metaclasses only when class creation itself must be customized.

Examples

- ORM frameworks
- Django Models
- SQLAlchemy
- Validation
- Plugin registration
- API generation

---

Avoid metaclasses when

- decorators solve the problem
- inheritance is sufficient
- composition works

---

## Interview Rule

If you're unsure whether to use a metaclass,

the answer is probably **don't**.

---

# Q48. Abstract Base Classes vs Protocols.

## ABC

```python
from abc import ABC, abstractmethod

class Animal(ABC):

    @abstractmethod

    def speak(self):
        ...
```

Subclass must implement.

---

## Protocol

```python
from typing import Protocol

class Animal(Protocol):

    def speak(self):
        ...
```

No inheritance required.

---

## Comparison

| ABC | Protocol |
|------|----------|
| Explicit inheritance | Structural typing |
| Runtime check | Static typing |
| Stronger contracts | Flexible |

---

# Q49. Duck typing vs static typing.

Duck Typing

> If it behaves like a duck,
> treat it like a duck.

```python
class Duck:

    def speak(self):

        print("Quack")
```

Python only checks behavior.

---

Static Typing

```python
def greet(name:str)->str:

    return name
```

Type checker verifies usage.

---

## Comparison

| Duck Typing | Static Typing |
|--------------|--------------|
| Runtime | Compile-time tools |
| Flexible | Safer |
| Dynamic | Predictable |

---

# Q50. Explain composition vs inheritance with trade-offs.

## Inheritance

Represents

```
IS-A
```

Example

```python
class Animal:
    ...

class Dog(Animal):
    ...
```

Dog **is an** Animal.

---

## Composition

Represents

```
HAS-A
```

Example

```python
class Engine:
    ...

class Car:

    def __init__(self):

        self.engine=Engine()
```

Car **has an** Engine.

---

## Comparison

| Inheritance | Composition |
|--------------|------------|
| Tight coupling | Loose coupling |
| Code reuse | Better flexibility |
| Harder to change | Easier to maintain |
| Supports polymorphism | Supports modularity |

---

## Best Practice

Prefer **composition over inheritance** unless there is a true **IS-A** relationship.

Examples

Good

```
Car HAS Engine
```

Bad

```
Car IS Engine
```

---

# Module Summary

Topics Covered

- Python Object Model
- `__new__` vs `__init__`
- Method Resolution Order (MRO)
- Diamond Inheritance
- C3 Linearization
- Instance, Class, and Static Methods
- Descriptors
- Data vs Non-data Descriptors
- Manual `property` Implementation
- `__slots__`
- Metaclasses
- Abstract Base Classes
- Protocols
- Duck Typing
- Composition vs Inheritance

---

# Common Interview Follow-up Questions

1. How does `super()` work internally?
2. Why does Python use C3 linearization instead of DFS or BFS?
3. Can `__new__()` return an object of another class?
4. How does attribute lookup work internally?
5. What is the descriptor lookup precedence?
6. How are methods bound to objects?
7. What is a bound method vs an unbound method?
8. How does Python implement `property` internally?
9. What happens if `__slots__` and `__dict__` are used together?
10. How does `type()` create a class dynamically?

# Python Interview Master Guide
# Module 04A – OOP Deep Dive (Advanced Interview Follow-up Questions)

These are advanced interview questions commonly asked in Senior Python interviews at companies like Google, Meta, Apple, Amazon, Microsoft, Stripe, Netflix, Uber, and Bloomberg.

These questions focus on **Python internals**, **CPython implementation**, and **object model design**.

---

# Q1. How does `super()` work internally?

## Short Answer

`super()` **does not simply call the parent class**.

Instead, it searches the **next class in the Method Resolution Order (MRO)**.

This is one of the biggest misconceptions in Python interviews.

---

## Example

```python
class A:
    def show(self):
        print("A")


class B(A):
    def show(self):
        print("B")
        super().show()


class C(A):
    def show(self):
        print("C")
        super().show()


class D(B, C):
    def show(self):
        print("D")
        super().show()


D().show()
```

Output

```
D
B
C
A
```

---

## Why?

MRO

```
D

↓

B

↓

C

↓

A

↓

object
```

When inside `B`:

```python
super()
```

does **not** mean

```
Call A
```

Instead it means

```
Continue searching after B
```

The next class is

```
C
```

---

## Internal Representation

`super()` stores

```
Current Class

+

Current Object

↓

super object
```

Memory

```
super

↓

Current Class

↓

Current Instance

↓

MRO Iterator
```

---

## CPython

Internally

```
super()

↓

PySuper_Type
```

It searches

```
instance.__class__.__mro__
```

starting **after the current class**.

---

## Interview Tip

> `super()` follows **MRO**, not direct parent relationships.

---

# Q2. Why does Python use C3 linearization instead of DFS or BFS?

## Problem

Multiple inheritance introduces ambiguity.

Example

```python
class A:
    pass


class B(A):
    pass


class C(A):
    pass


class D(B, C):
    pass
```

Hierarchy

```
      A
     / \
    B   C
     \ /
      D
```

---

## DFS Problem

Depth-First Search might produce

```
D

↓

B

↓

A

↓

C
```

Now

```
A

appears before

C
```

which violates inheritance ordering.

---

## BFS Problem

Breadth-First Search might produce

```
D

↓

B

↓

C

↓

A
```

This works here but fails for more complex hierarchies because it cannot consistently preserve local precedence.

---

## C3 Goals

C3 guarantees:

- Monotonicity
- Local precedence order
- Deterministic lookup
- No duplicate classes

---

## Example

```python
class D(B, C):
    pass
```

MRO

```
D

↓

B

↓

C

↓

A

↓

object
```

---

## Interview Tip

Python uses C3 because it guarantees a **consistent and conflict-free method resolution order**.

---

# Q3. Can `__new__()` return an object of another class?

## Yes.

Unlike `__init__()`, `__new__()` can return **any object**.

---

## Example

```python
class A:

    def __new__(cls):

        return "Hello"


obj = A()

print(obj)
print(type(obj))
```

Output

```
Hello

<class 'str'>
```

---

## Another Example

```python
class Person:
    pass


class Employee:

    def __new__(cls):

        return Person()


obj = Employee()

print(type(obj))
```

Output

```
<class '__main__.Person'>
```

---

## Important Rule

If `__new__()` returns an object that is **not an instance of the class**, Python skips `__init__()`.

Example

```python
class Test:

    def __new__(cls):
        return 10

    def __init__(self):
        print("Init")
```

Output

```
10
```

`__init__()` is never executed.

---

## Real-world Uses

- Singleton pattern
- Immutable classes
- Object pooling
- Factory objects

---

# Q4. How does attribute lookup work internally?

Every attribute access

```python
obj.name
```

calls

```python
obj.__getattribute__("name")
```

---

## Lookup Order

```
1. Data Descriptor

↓

2. Instance __dict__

↓

3. Non-data Descriptor

↓

4. Class __dict__

↓

5. Parent Classes (MRO)

↓

6. __getattr__()
```

---

## Example

```python
class User:

    role = "Admin"

u = User()

print(u.role)
```

Python searches

```
u.__dict__

↓

User.__dict__

↓

object
```

---

## CPython

Internally

```
PyObject_GenericGetAttr()
```

implements attribute lookup.

---

## Interview Tip

Almost every

```
obj.attribute
```

internally becomes

```
obj.__getattribute__()
```

---

# Q5. What is the descriptor lookup precedence?

Python follows a strict precedence.

```
Data Descriptor

↓

Instance Dictionary

↓

Non-data Descriptor

↓

Class Dictionary

↓

Parent Classes
```

---

## Data Descriptor

Has

```
__get__

+

__set__
```

Example

```
property
```

---

## Non-data Descriptor

Only

```
__get__
```

Example

Functions

---

## Why?

Data descriptors control both reading and writing, so they always take priority over instance attributes.

---

## Example

```python
class Descriptor:

    def __get__(self,obj,objtype):
        return "Descriptor"

    def __set__(self,obj,value):
        pass


class Test:

    value = Descriptor()


t = Test()

t.__dict__["value"] = 100

print(t.value)
```

Output

```
Descriptor
```

The data descriptor overrides the instance attribute.

---

# Q6. How are methods bound to objects?

Functions become methods through the **descriptor protocol**.

---

## Example

```python
class User:

    def greet(self):
        print("Hello")


u = User()

print(u.greet)
```

Output

```
<bound method User.greet of ...>
```

---

## What Happens?

```
User.greet

↓

Function Object

↓

Descriptor

↓

Creates Bound Method

↓

Stores

self

+

function
```

---

## Internal Representation

```
Bound Method

↓

Function Pointer

↓

Instance Pointer
```

---

## CPython

Implemented using

```
PyMethodObject
```

---

# Q7. What is a bound method vs an unbound method?

## Bound Method

Belongs to an instance.

```python
u = User()

u.greet
```

Contains

```
Function

+

self
```

Calling

```python
u.greet()
```

automatically passes

```
self
```

---

## Unbound Method

Accessed from the class.

```python
User.greet
```

Returns the raw function.

Calling

```python
User.greet()
```

raises

```
TypeError
```

because

```
self
```

is missing.

Correct

```python
User.greet(u)
```

---

## Python 3 Note

Python 3 no longer has a dedicated "unbound method" type. Accessing a method on the class returns the original function object.

---

## Comparison

| Bound Method | Unbound Function |
|--------------|------------------|
| Has instance | No instance |
| Receives `self` automatically | Caller supplies `self` |
| Accessed via object | Accessed via class |

---

# Q8. How does Python implement `property` internally?

`property` is implemented as a **data descriptor**.

---

## Simplified Version

```python
class Property:

    def __init__(self, getter):
        self.getter = getter

    def __get__(self, obj, objtype):
        return self.getter(obj)
```

---

Usage

```python
class User:

    def __init__(self):
        self._name = "Monika"

    @Property
    def name(self):
        return self._name
```

---

## Built-in property

The real implementation also supports

- getter
- setter
- deleter
- docstring

---

Memory

```
property object

↓

Getter

↓

Setter

↓

Deleter
```

---

## Interview Tip

`property` is **not** syntax sugar—it is a descriptor object.

---

# Q9. What happens if `__slots__` and `__dict__` are used together?

Normally,

`__slots__` removes the instance dictionary to save memory.

Example

```python
class User:

    __slots__ = ("name",)
```

Now

```python
u = User()

u.age = 20
```

raises

```
AttributeError
```

---

## Adding `__dict__`

```python
class User:

    __slots__ = ("name", "__dict__")
```

Now

```python
u = User()

u.age = 20

print(u.age)
```

Output

```
20
```

---

## Memory

Without `__dict__`

```
Object

↓

Slots Only
```

With `__dict__`

```
Object

↓

Slots

+

Dictionary
```

---

## Trade-off

Advantages

- Fixed attributes remain efficient.
- Dynamic attributes become possible.

Disadvantages

- Increased memory usage.
- Reduced benefits of `__slots__`.

---

# Q10. How does `type()` create a class dynamically?

Classes are objects created by the metaclass `type`.

---

## Normal Syntax

```python
class User:
    pass
```

Equivalent to

```python
User = type(
    "User",
    (),
    {}
)
```

---

## Adding Methods Dynamically

```python
def greet(self):
    print("Hello")


User = type(
    "User",
    (),
    {
        "greet": greet
    }
)

u = User()

u.greet()
```

Output

```
Hello
```

---

## Parameters

```python
type(
    class_name,
    base_classes,
    class_dictionary
)
```

---

## Internal Flow

```
type()

↓

Prepare Namespace

↓

Execute Class Body

↓

Create Class Object

↓

Return Class
```

---

## CPython

Internally

```
PyType_Type
```

creates new classes.

Every class in Python is ultimately an instance of `type`.

Example

```python
print(type(int))
print(type(list))
print(type(dict))
```

Output

```
<class 'type'>
<class 'type'>
<class 'type'>
```

---

# Summary

This module covered advanced OOP interview topics:

- Internal working of `super()`
- Why Python uses C3 linearization
- Returning different objects from `__new__()`
- Attribute lookup algorithm
- Descriptor lookup precedence
- Method binding via the descriptor protocol
- Bound methods vs class functions
- Internal implementation of `property`
- Using `__slots__` with `__dict__`
- Dynamic class creation with `type()`

These topics are commonly used to assess deep knowledge of Python's object model and are frequently discussed in senior backend engineering interviews.