# Python OOP Interview Guide (Part 1)

# 1. What is Object-Oriented Programming (OOP)?

## Definition

Object-Oriented Programming (OOP) is a programming paradigm where software is organized around **objects** instead of functions.

An object represents a **real-world entity** that contains:

- **State (Data / Attributes)**
- **Behavior (Methods / Functions)**

Instead of writing separate functions and data, OOP combines them together inside classes.

---

## Real-world Example

Consider a Car.

A car has:

**Attributes**
- Color
- Brand
- Speed

**Behaviors**
- Start()
- Stop()
- Accelerate()

In Python:

```python
class Car:
    def __init__(self, brand, color):
        self.brand = brand
        self.color = color

    def start(self):
        print("Car Started")

car = Car("BMW", "Black")
car.start()
```

Output

```
Car Started
```

---

## Why Objects?

Instead of writing

```python
brand = "BMW"
color = "Black"

def start():
    pass
```

we write

```python
car = Car("BMW", "Black")
```

Everything related to a car stays together.

---

## Advantages

- Better organization
- Easier maintenance
- Reusable code
- Better scalability
- Easy testing
- Real-world modeling

---

# Interview Answer (Short)

> Object-Oriented Programming is a programming paradigm that models software using objects. Each object contains state (attributes) and behavior (methods). OOP improves modularity, reusability, maintainability, and scalability.

---

# 2. Why do we need OOP when procedural programming already exists?

## Procedural Programming

Procedural programming focuses on functions.

Example

```python
balance = 1000

def withdraw(amount):
    global balance
    balance -= amount
```

Everything is global.

Large projects become difficult to manage.

Problems:

- Global variables
- Code duplication
- Difficult maintenance
- Low scalability
- Poor abstraction
- Hard testing

---

## OOP Solution

```python
class BankAccount:

    def __init__(self, balance):
        self.balance = balance

    def withdraw(self, amount):
        self.balance -= amount
```

Now balance belongs only to that account.

Each account manages itself.

---

## Example

Without OOP

```
balance1
balance2
balance3

withdraw(balance1)
withdraw(balance2)
withdraw(balance3)
```

With OOP

```
account1.withdraw()
account2.withdraw()
account3.withdraw()
```

Cleaner and scalable.

---

## Large Company Example

Imagine Amazon.

Procedural

```
Customer Functions
Order Functions
Payment Functions
Cart Functions
```

Thousands of functions become impossible to maintain.

OOP

```
Customer Class
Order Class
Cart Class
Payment Class
Product Class
```

Each class manages itself.

---

## Interview Answer

> Procedural programming works well for small applications, but as projects grow, managing global data and functions becomes difficult. OOP solves this by encapsulating data and behavior into objects, making software modular, reusable, secure, and easier to maintain.

---

# 3. What are the Four Pillars of OOP?

The four pillars are:

1. Encapsulation
2. Abstraction
3. Inheritance
4. Polymorphism

---

# 1. Encapsulation

Binding data and methods together while restricting direct access.

Example

```python
class Bank:

    def __init__(self):
        self.__balance = 1000

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance
```

Cannot access

```python
bank.__balance
```

Must use

```python
bank.get_balance()
```

Benefits

- Data protection
- Controlled access
- Better security

---

# 2. Abstraction

Showing only necessary details while hiding implementation.

Example

Driving a car.

You only use

```
Brake
Accelerator
Steering
```

You don't know engine internals.

Python Example

```python
from abc import ABC, abstractmethod

class Shape(ABC):

    @abstractmethod
    def area(self):
        pass

class Circle(Shape):

    def area(self):
        return 3.14
```

---

# 3. Inheritance

Allows one class to reuse another class.

```python
class Animal:

    def speak(self):
        print("Animal")

class Dog(Animal):
    pass

dog = Dog()
dog.speak()
```

Output

```
Animal
```

Benefits

- Code reuse
- Extensibility
- Cleaner design

---

# 4. Polymorphism

One interface, many implementations.

```python
class Dog:

    def speak(self):
        print("Bark")

class Cat:

    def speak(self):
        print("Meow")

animals = [Dog(), Cat()]

for animal in animals:
    animal.speak()
```

Output

```
Bark
Meow
```

Same method name.

Different behavior.

---

## Interview Answer

> The four pillars of OOP are Encapsulation, Abstraction, Inheritance, and Polymorphism. Together they improve code reuse, flexibility, maintainability, and security.

---

# 4. Difference between Class and Object

## Class

Blueprint.

Defines attributes and methods.

Consumes no significant memory until objects are created.

Example

```python
class Student:
    pass
```

---

## Object

Actual instance of a class.

Consumes memory.

```python
s1 = Student()
```

---

## Diagram

```
          Class

        Student
     ----------------

        |
        |

   ------------------
   |       |        |

 Student Student Student
   s1      s2      s3
```

---

## Memory

```
Class
↓

Stored once

↓

Objects

Stored multiple times
```

---

## Interview Answer

| Class | Object |
|--------|---------|
| Blueprint | Instance |
| Logical entity | Physical entity |
| Created once | Multiple objects |
| Defines behavior | Uses behavior |
| No instance data | Holds actual data |

---

# 5. What happens internally when an object is created in Python?

Suppose

```python
class Employee:

    def __init__(self, name):
        self.name = name

emp = Employee("Alice")
```

Internally

```
Step 1

Employee()

↓

type.__call__()

↓

Step 2

__new__()

↓

Memory allocated

↓

Returns object

↓

Step 3

__init__()

↓

Initialize attributes

↓

Object returned
```

Sequence

```
Employee()

↓

type.__call__()

↓

__new__()

↓

Memory allocation

↓

__init__()

↓

Ready object
```

---

## Internals

Python actually executes

```python
obj = Employee.__new__(Employee)

Employee.__init__(obj, "Alice")
```

---

## Interview Answer

> Object creation starts when the class is called. Python internally invokes `type.__call__()`, which first calls `__new__()` to allocate memory and create the object, then calls `__init__()` to initialize it before returning the fully constructed instance.

---

# 6. What is `self`? Why is it required?

## Definition

`self` represents the **current instance** of a class.

It allows methods to access:

- Instance variables
- Other instance methods

---

## Example

```python
class Student:

    def __init__(self, name):
        self.name = name

    def display(self):
        print(self.name)

student = Student("John")
student.display()
```

Output

```
John
```

---

## What Python Does Internally

When you call:

```python
student.display()
```

Python converts it to:

```python
Student.display(student)
```

The instance (`student`) is passed automatically as the first argument, which is conventionally named `self`.

---

## Why is `self` Required?

Without `self`, Python wouldn't know which object's data to access.

Example:

```python
s1 = Student("Alice")
s2 = Student("Bob")
```

Calling:

```python
s1.display()
```

prints `Alice`, while:

```python
s2.display()
```

prints `Bob`.

`self` identifies the current object.

---

## Interview Answer

> `self` is a reference to the current instance of a class. Python automatically passes it as the first argument to instance methods, allowing access to instance variables and other instance methods.

---

# 7. Difference between `__new__()` and `__init__()`

| `__new__()` | `__init__()` |
|--------------|--------------|
| Creates the object | Initializes the object |
| Called first | Called after `__new__()` |
| Static method by nature | Instance method |
| Returns the new object | Returns `None` |
| Responsible for memory allocation | Responsible for attribute initialization |
| Can return a different object | Cannot replace the object |

---

## Example

```python
class Demo:

    def __new__(cls):
        print("Creating object")
        return super().__new__(cls)

    def __init__(self):
        print("Initializing object")

obj = Demo()
```

Output

```
Creating object
Initializing object
```

---

## When to Override `__new__()`

Rare use cases:

- Singleton pattern
- Immutable types (`int`, `str`, `tuple`)
- Object caching
- Metaclass implementations

---

## Interview Answer

> `__new__()` is responsible for creating and returning a new object, while `__init__()` initializes the object's attributes after creation. `__new__()` executes before `__init__()`.

---

# 8. What is Object Identity, Type, and Value?

Every Python object has three fundamental properties:

## Identity

A unique identifier for the object during its lifetime.

```python
x = [1, 2]
print(id(x))
```

`id(x)` returns the object's identity.

---

## Type

Specifies the class or data type of the object.

```python
print(type(x))
```

Output

```
<class 'list'>
```

---

## Value

The actual data stored in the object.

```python
x = [1, 2]
print(x)
```

Value:

```
[1, 2]
```

---

## Example

```python
x = [1, 2]
y = x
```

- `x` and `y` have the **same identity**
- Same type (`list`)
- Same value (`[1, 2]`)

---

## Interview Answer

> Every Python object has an identity (unique memory reference), a type (its class), and a value (the data it holds). Identity is obtained using `id()`, type using `type()`, and value by evaluating the object itself.

---

# 9. Instance State vs Class State

## Instance State

Belongs to each object individually.

```python
class Employee:

    def __init__(self, name):
        self.name = name
```

Each object has its own `name`.

---

## Class State

Shared across all instances.

```python
class Employee:

    company = "Google"
```

Every object shares the same `company`.

---

## Example

```python
class Employee:

    company = "Google"

    def __init__(self, name):
        self.name = name

e1 = Employee("Alice")
e2 = Employee("Bob")
```

- `e1.name` → `Alice`
- `e2.name` → `Bob`
- `e1.company` → `Google`
- `e2.company` → `Google`

---

## Interview Answer

| Instance State | Class State |
|----------------|-------------|
| Unique to each object | Shared across all objects |
| Stored in instance namespace | Stored in class namespace |
| Modified independently | Shared until overridden |

---

# 10. What is the Lifecycle of a Python Object?

A Python object goes through several stages:

```
Class Definition
        │
        ▼
Object Creation
(__new__)
        │
        ▼
Initialization
(__init__)
        │
        ▼
Usage
(method calls, attribute access)
        │
        ▼
Reference Count Changes
        │
        ▼
Garbage Collection
(__del__ may run if defined)
        │
        ▼
Memory Released
```

---

## Detailed Steps

### 1. Class Definition

Python creates the class object.

### 2. Object Creation

`__new__()` allocates memory and returns a new instance.

### 3. Initialization

`__init__()` sets up the object's attributes.

### 4. Usage

The object is used by the program.

### 5. Reference Counting

Python tracks how many references point to the object.

### 6. Garbage Collection

When the reference count drops to zero, CPython typically deallocates the object immediately. Python's cyclic garbage collector also detects and frees groups of objects involved in reference cycles.

### 7. Destruction

If a `__del__()` method is defined, it may be called before the object's memory is reclaimed. However, the timing of `__del__()` is not guaranteed across all Python implementations, so it should not be relied upon for critical resource cleanup.

---

## Interview Answer

> A Python object's lifecycle consists of class creation, object allocation (`__new__()`), initialization (`__init__()`), normal usage, reference counting, garbage collection, optional finalization (`__del__()`), and memory deallocation. In CPython, objects are primarily managed through reference counting with cyclic garbage collection for reference cycles.

---

# Key Takeaways

- OOP organizes code around objects containing data and behavior.
- OOP improves modularity, reuse, maintainability, and scalability.
- The four pillars are Encapsulation, Abstraction, Inheritance, and Polymorphism.
- A class is a blueprint; an object is its runtime instance.
- Object creation follows the sequence: `type.__call__()` → `__new__()` → `__init__()`.
- `self` refers to the current instance and is passed automatically to instance methods.
- `__new__()` creates an object; `__init__()` initializes it.
- Every object has an identity, type, and value.
- Instance state belongs to individual objects; class state is shared.
- Python objects are managed through reference counting and garbage collection.