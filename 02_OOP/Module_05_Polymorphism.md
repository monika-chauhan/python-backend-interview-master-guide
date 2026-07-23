# Python OOP Interview Guide (Module 5)

# Polymorphism

---

# 51. What is Polymorphism?

## Definition

The word **Polymorphism** comes from two Greek words:

- **Poly** → Many
- **Morph** → Forms

So polymorphism means:

> **One interface, many implementations.**

The same method, function, or operator behaves differently depending on the object it operates on.

It is one of the **four pillars of Object-Oriented Programming (OOP).**

---

## Real-world Example

Think about a **Payment System**.

```
payment.pay()
```

Depending on the object:

```
CreditCard → Processes card payment

UPI → Processes UPI payment

NetBanking → Processes bank payment

Wallet → Processes wallet payment
```

The method name remains the same:

```
pay()
```

Behavior changes.

---

## Python Example

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

Same interface.

Different implementation.

---

## Diagram

```
          speak()

             |

     -----------------

     |               |

   Dog             Cat

 Bark()          Meow()
```

---

## Interview Answer

> Polymorphism is an OOP concept where the same interface can have multiple implementations. The behavior of a method depends on the object invoking it, enabling flexibility and extensibility.

---

# 52. Compile-time vs Runtime Polymorphism

## Compile-time Polymorphism

The method to execute is determined during compilation.

Examples in languages like:

- C++
- Java

Achieved using:

- Method Overloading
- Operator Overloading (C++)

Example (Java)

```java
add(int a, int b)

add(int a, int b, int c)
```

Compiler selects the correct method.

---

## Runtime Polymorphism

The method is selected during execution.

Achieved through:

- Method Overriding
- Dynamic Dispatch

Python supports **runtime polymorphism**.

Example

```python
class Animal:

    def speak(self):
        pass


class Dog(Animal):

    def speak(self):
        print("Bark")


class Cat(Animal):

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

---

## Comparison

| Compile-time | Runtime |
|--------------|---------|
| Decided during compilation | Decided during execution |
| Faster dispatch | Slightly more flexible |
| Uses overloading | Uses overriding |
| Common in Java/C++ | Primary mechanism in Python |

---

## Interview Answer

> Compile-time polymorphism resolves method calls during compilation, typically through method overloading. Runtime polymorphism resolves method calls during execution through method overriding and dynamic dispatch. Python primarily supports runtime polymorphism.

---

# 53. Does Python Support Method Overloading?

## Short Answer

**No.**

Python does **not** support traditional method overloading.

---

## Example

```python
class Demo:

    def add(self, a):
        print(a)

    def add(self, a, b):
        print(a + b)
```

Only the second method exists.

Calling

```python
d = Demo()

d.add(5)
```

Results in

```
TypeError
```

The first definition is overwritten.

---

## Why?

Python stores methods in a class dictionary.

Later definitions replace earlier ones.

---

## Alternatives

### Default Arguments

```python
def add(a, b=0):
    return a + b
```

---

### Variable Arguments

```python
def add(*numbers):
    return sum(numbers)
```

---

### `functools.singledispatch`

```python
from functools import singledispatch

@singledispatch
def process(value):
    print(value)
```

Useful for type-based dispatch on the first argument.

---

## Interview Answer

> Python does not support traditional method overloading because later method definitions overwrite earlier ones. Similar behavior is achieved using default arguments, variable-length arguments, or utilities like `functools.singledispatch`.

---

# 54. Why Doesn't Python Support Traditional Overloading?

Python is:

- Dynamically typed
- Interpreted
- Simple by design

---

Example

```python
def add(a, b):
    return a + b
```

Works for

```python
add(5, 10)

add("A", "B")

add([1], [2])
```

Same function.

Different data types.

No overloading needed.

---

## Design Philosophy

Python follows:

> "There should be one—and preferably only one—obvious way to do it."

Instead of multiple overloaded methods, Python encourages flexible function signatures.

---

## Interview Answer

> Python avoids traditional method overloading because it is dynamically typed. A single function can operate on different data types, and flexible parameter mechanisms such as default values and `*args` reduce the need for multiple overloaded methods.

---

# 55. Method Overriding

## Definition

Method overriding occurs when a child class provides its own implementation of a method already defined in the parent class.

---

## Example

```python
class Animal:

    def speak(self):
        print("Animal Sound")


class Dog(Animal):

    def speak(self):
        print("Bark")


dog = Dog()

dog.speak()
```

Output

```
Bark
```

---

## Using `super()`

```python
class Animal:

    def speak(self):
        print("Animal")


class Dog(Animal):

    def speak(self):
        super().speak()
        print("Bark")
```

Output

```
Animal
Bark
```

---

## Interview Answer

> Method overriding allows a subclass to provide a specialized implementation of a method inherited from its parent class. It is the foundation of runtime polymorphism.

---

# 56. Operator Overloading

## Definition

Operator overloading allows operators to behave differently for user-defined objects.

Python achieves this using **magic (dunder) methods**.

---

## Example

```python
class Point:

    def __init__(self, x):
        self.x = x

    def __add__(self, other):
        return Point(self.x + other.x)

    def __str__(self):
        return str(self.x)


p1 = Point(10)

p2 = Point(20)

print(p1 + p2)
```

Output

```
30
```

---

## Common Operator Methods

| Operator | Magic Method |
|-----------|--------------|
| + | `__add__()` |
| - | `__sub__()` |
| * | `__mul__()` |
| / | `__truediv__()` |
| == | `__eq__()` |
| < | `__lt__()` |
| > | `__gt__()` |
| [] | `__getitem__()` |

---

## Interview Answer

> Operator overloading allows built-in operators to work with user-defined objects by implementing special methods such as `__add__()`, `__eq__()`, and `__lt__()`.

---

# 57. Duck Typing

## Definition

Duck typing is a Python concept where an object's suitability is determined by the presence of required methods rather than its type.

> "If it walks like a duck and quacks like a duck, treat it as a duck."

---

## Example

```python
class Dog:

    def speak(self):
        print("Bark")


class Human:

    def speak(self):
        print("Hello")


def talk(obj):
    obj.speak()


talk(Dog())

talk(Human())
```

Output

```
Bark
Hello
```

The function only requires that the object has a `speak()` method.

---

## Benefits

- Flexible code
- Loose coupling
- Easier extension
- Follows Python's dynamic nature

---

## Interview Answer

> Duck typing means Python focuses on an object's behavior rather than its type. If an object provides the required methods or attributes, it can be used regardless of its class hierarchy.

---

# 58. Real-world Example of Polymorphism

## Payment Gateway

```python
class Payment:

    def pay(self):
        raise NotImplementedError


class CreditCard(Payment):

    def pay(self):
        print("Credit Card Payment")


class UPI(Payment):

    def pay(self):
        print("UPI Payment")


class Wallet(Payment):

    def pay(self):
        print("Wallet Payment")


payments = [
    CreditCard(),
    UPI(),
    Wallet()
]

for payment in payments:
    payment.pay()
```

Output

```
Credit Card Payment
UPI Payment
Wallet Payment
```

---

## Other Examples

- Notification Systems (`send()`)
- Database Drivers (`connect()`)
- Cloud Providers (`upload()`)
- Logging Frameworks (`log()`)
- Machine Learning Models (`predict()`)

---

## Interview Answer

> A payment gateway is a common example of polymorphism. Different payment methods implement the same `pay()` interface, allowing the application to process payments without knowing the specific implementation.

---

# 59. Function Polymorphism

Python's built-in functions are naturally polymorphic.

---

## `len()`

```python
print(len("Python"))

print(len([1, 2, 3]))

print(len({"a": 1}))
```

Output

```
6
3
1
```

---

## `+`

```python
print(10 + 20)

print("Hello " + "World")

print([1] + [2])
```

Output

```
30
Hello World
[1, 2]
```

---

## `max()`

```python
print(max(1, 2, 3))

print(max("apple", "banana"))
```

The same function works with different types.

---

## Custom Example

```python
def display(value):
    print(value)


display(100)

display("Python")

display([1, 2, 3])
```

---

## Interview Answer

> Function polymorphism allows the same function to operate on different types of objects. Python's built-in functions such as `len()`, `max()`, and the `+` operator demonstrate this behavior.

---

# 60. Benefits of Polymorphism

## 1. Code Reusability

One interface supports multiple implementations.

---

## 2. Extensibility

Add new subclasses without changing existing code.

Example

```
Payment

↓

GooglePay

↓

ApplePay

↓

CreditCard
```

The calling code remains unchanged.

---

## 3. Loose Coupling

Client code depends on interfaces rather than concrete implementations.

---

## 4. Better Maintainability

Changes in one implementation do not affect others if the interface remains consistent.

---

## 5. Cleaner APIs

A single method name (`pay()`, `save()`, `send()`) works across many implementations.

---

## 6. Supports Open/Closed Principle

Classes are:

- Open for extension
- Closed for modification

You can add new behavior by introducing new subclasses instead of editing existing code.

---

## Enterprise Example

```
Notification

↓

Email

↓

SMS

↓

Slack

↓

Teams
```

Application code simply calls:

```python
notification.send()
```

---

## Interview Answer

> Polymorphism improves flexibility, code reuse, maintainability, and extensibility. It enables software to work with different object types through a common interface, making systems easier to extend and maintain.

---

# Module Summary

- Polymorphism means **one interface, many implementations**.
- Python primarily supports **runtime polymorphism** through **method overriding**.
- Python does **not** support traditional method overloading; use default arguments, `*args`, or `functools.singledispatch` instead.
- Method overriding allows subclasses to redefine inherited behavior.
- Operator overloading is implemented using special (dunder) methods like `__add__()` and `__eq__()`.
- Duck typing emphasizes an object's behavior rather than its type.
- Built-in functions such as `len()`, `max()`, and the `+` operator demonstrate function polymorphism.
- Polymorphism promotes reusable, extensible, loosely coupled, and maintainable software designs.