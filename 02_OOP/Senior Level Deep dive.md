# Python OOP Interview Guide (Module 11)

# Senior-Level Deep Dive

---

# 141. Explain "Favor Composition over Inheritance"

## Definition

**"Favor Composition over Inheritance"** is one of the most important principles in object-oriented design.

It means:

> **Instead of inheriting functionality from another class, build classes by combining smaller, reusable objects.**

Composition creates **HAS-A** relationships, whereas inheritance creates **IS-A** relationships.

Modern frameworks like **Django, FastAPI, Spring Boot, .NET**, and **Kubernetes operators** heavily rely on composition.

---

## Inheritance Example

```python
class Engine:

    def start(self):
        print("Engine Started")


class Car(Engine):
    pass
```

Problem:

A Car is **not** an Engine.

Wrong relationship.

---

## Composition Example

```python
class Engine:

    def start(self):
        print("Engine Started")


class Car:

    def __init__(self):
        self.engine = Engine()

    def start(self):
        self.engine.start()
```

```
Car

HAS-A

Engine
```

---

## Why Composition?

### Loose Coupling

Components can be replaced independently.

---

### Better Testing

```python
service = UserService(MockRepository())
```

---

### Reusable Components

```
EmailService

↓

UserService

↓

OrderService

↓

InvoiceService
```

---

### Avoids Deep Hierarchies

Instead of

```
Vehicle

↓

Car

↓

ElectricCar

↓

LuxuryElectricCar
```

Use independent components.

---

## Enterprise Example

```
OrderService

HAS-A

InventoryService

↓

PaymentService

↓

NotificationService

↓

AuditService
```

---

## Interview Answer

> Composition is preferred over inheritance because it promotes loose coupling, modularity, and flexibility. Instead of extending classes, objects collaborate by containing other objects, making systems easier to test, maintain, and extend.

---

# 142. Why is Multiple Inheritance Controversial?

Python supports multiple inheritance.

Example:

```python
class A:
    pass


class B:
    pass


class C(A, B):
    pass
```

---

## Advantages

- Code reuse
- Mixins
- Multiple behaviors

---

## Problems

### Diamond Problem

```
        A

      /   \

     B     C

      \   /

        D
```

Which parent's method should `D` inherit?

---

### Ambiguous Behavior

Two parents may define the same method.

```python
class A:

    def show(self):
        print("A")


class B:

    def show(self):
        print("B")


class C(A, B):
    pass


C().show()
```

Output

```
A
```

Determined by the Method Resolution Order (MRO).

---

## Best Practice

Use multiple inheritance primarily for **mixins** that provide small, focused behaviors.

---

## Interview Answer

> Multiple inheritance can lead to ambiguity, tight coupling, and complex class hierarchies. Python resolves conflicts using the Method Resolution Order (MRO), but composition is often a simpler and more maintainable alternative.

---

# 143. How Does Python Implement MRO Internally?

## MRO (Method Resolution Order)

MRO defines the order in which Python searches classes for methods and attributes.

Python uses the **C3 Linearization Algorithm** to compute a consistent order.

---

## Example

```python
class A:
    pass


class B(A):
    pass


class C(A):
    pass


class D(B, C):
    pass


print(D.mro())
```

Output

```python
[
    D,
    B,
    C,
    A,
    object
]
```

---

## Attribute Lookup

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

Python searches each class in order until it finds the requested attribute.

---

## Why C3 Linearization?

It guarantees:

- Consistent ordering
- Monotonicity
- No duplicate visits
- Predictable resolution

---

## Interview Answer

> Python computes the Method Resolution Order using the C3 Linearization algorithm. The MRO determines the order in which base classes are searched for attributes and methods, ensuring a consistent and predictable lookup in multiple inheritance hierarchies.

---

# 144. Explain Descriptors with `@property`

## What is a Descriptor?

A descriptor is any object that defines one or more of these methods:

```python
__get__()

__set__()

__delete__()
```

Descriptors customize attribute access.

---

## `@property` is a Descriptor

Example:

```python
class Employee:

    def __init__(self):
        self._salary = 0

    @property
    def salary(self):
        return self._salary

    @salary.setter
    def salary(self, value):
        if value < 0:
            raise ValueError("Salary cannot be negative")
        self._salary = value
```

---

## Internally

```python
emp.salary
```

becomes roughly

```python
Employee.salary.__get__(emp, Employee)
```

and

```python
emp.salary = 100
```

becomes roughly

```python
Employee.salary.__set__(emp, 100)
```

---

## Uses

- Validation
- Lazy loading
- Computed attributes
- ORM fields (e.g., Django, SQLAlchemy)

---

## Interview Answer

> Descriptors are objects that control attribute access using `__get__()`, `__set__()`, and `__delete__()`. The `@property` decorator is implemented using the descriptor protocol, enabling validation and computed attributes while preserving attribute-style access.

---

# 145. How Does Attribute Lookup Work Internally?

When you access:

```python
obj.name
```

Python performs the following lookup sequence:

---

## Step 1

Call:

```python
type(obj).__getattribute__(obj, "name")
```

---

## Step 2

Search for a **data descriptor** (implements `__set__` or `__delete__`) in the class.

---

## Step 3

Search the instance dictionary:

```python
obj.__dict__
```

---

## Step 4

Search for a **non-data descriptor** or class attribute.

---

## Step 5

Search base classes following the MRO.

---

## Step 6

If not found, call:

```python
__getattr__()
```

---

## Lookup Order

```
Data Descriptor

↓

Instance __dict__

↓

Non-data Descriptor / Class Attribute

↓

Base Classes (MRO)

↓

__getattr__()

↓

AttributeError
```

---

## Interview Answer

> Attribute lookup starts with `__getattribute__()`, checks data descriptors, then the instance dictionary, class attributes, base classes according to the MRO, and finally falls back to `__getattr__()` if the attribute is still not found.

---

# 146. Explain Object Creation Step by Step

When you write:

```python
user = User("Alice")
```

Python performs these steps:

---

## Step 1

Evaluate the class object:

```python
User
```

---

## Step 2

Call:

```python
User.__call__()
```

(`type.__call__()` by default)

---

## Step 3

Invoke:

```python
User.__new__()
```

to allocate memory and create the instance.

---

## Step 4

Invoke:

```python
User.__init__()
```

to initialize the instance.

---

## Step 5

Return the fully initialized object.

---

## Diagram

```
User()

↓

type.__call__()

↓

__new__()

↓

Memory Allocated

↓

__init__()

↓

Object Returned
```

---

## Interview Answer

> Object creation begins with `type.__call__()`, which invokes `__new__()` to allocate memory and create the instance, followed by `__init__()` to initialize it. The initialized object is then returned to the caller.

---

# 147. Explain Memory Layout of Python Objects

Every Python object contains metadata in addition to its actual data.

---

## Simplified Layout

```
+----------------------+
| Reference Count      |
+----------------------+
| Type Pointer         |
+----------------------+
| Object Data          |
+----------------------+
```

---

## Components

### Reference Count

Tracks how many references point to the object.

---

### Type Pointer

Points to the object's class.

---

### Object Data

Stores instance attributes and values.

---

## Instance Attributes

Most user-defined objects store attributes in:

```python
obj.__dict__
```

---

## Memory Optimizations

Using `__slots__` removes the per-instance `__dict__` (unless explicitly included), reducing memory usage and speeding up attribute access for many instances.

---

## Interview Answer

> A Python object stores reference count information, a pointer to its type, and its instance data. User-defined objects typically keep attributes in `__dict__`, while `__slots__` can reduce memory usage by avoiding the instance dictionary.

---

# 148. Explain Method Binding Internally

Methods are functions stored on the class.

When accessed through an instance, Python creates a **bound method**.

---

## Example

```python
class User:

    def greet(self):
        print("Hello")


user = User()

user.greet()
```

---

## Internally

```python
User.greet(user)
```

The instance is automatically passed as the first argument (`self`).

---

## Why?

Functions implement the descriptor protocol.

Accessing:

```python
user.greet
```

returns a **bound method**.

---

## Diagram

```
Class Function

↓

Descriptor

↓

Bound Method

↓

self Attached
```

---

## Interview Answer

> Instance methods are functions stored on the class. When accessed through an instance, the function's descriptor binds the instance to the function, creating a bound method that automatically receives `self`.

---

# 149. Explain Dynamic Dispatch in Python

## Definition

Dynamic dispatch means the method that executes is determined **at runtime**, based on the actual type of the object.

---

## Example

```python
class Animal:

    def speak(self):
        print("Animal")


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

## How It Works

At runtime, Python checks the object's type and follows its MRO to locate the appropriate implementation.

---

## Benefits

- Polymorphism
- Extensibility
- Loose coupling

---

## Interview Answer

> Dynamic dispatch is the runtime selection of the appropriate method implementation based on the object's actual type. It enables polymorphism by allowing the same method call to invoke different implementations.

---

# 150. Design a Scalable Notification System Using OOP Principles

## Requirements

Support multiple notification channels:

- Email
- SMS
- Push Notification
- Slack
- Microsoft Teams

The system should allow new channels to be added without modifying existing business logic.

---

## High-Level Architecture

```
Client

↓

NotificationController

↓

NotificationService

↓

NotificationChannel (Interface)

      │
 ┌────┼────┬─────────┬────────┐
 ▼    ▼    ▼         ▼        ▼
Email SMS Push     Slack    Teams

↓

Message Queue (Optional)

↓

Provider APIs
```

---

## Step 1: Notification Interface

```python
from abc import ABC, abstractmethod


class NotificationChannel(ABC):

    @abstractmethod
    def send(self, message, recipient):
        pass
```

---

## Step 2: Concrete Channels

```python
class EmailNotification(NotificationChannel):

    def send(self, message, recipient):
        print(f"Email sent to {recipient}")


class SMSNotification(NotificationChannel):

    def send(self, message, recipient):
        print(f"SMS sent to {recipient}")
```

---

## Step 3: Notification Service

```python
class NotificationService:

    def __init__(self, channel):
        self.channel = channel

    def notify(self, message, recipient):
        self.channel.send(message, recipient)
```

---

## Usage

```python
service = NotificationService(EmailNotification())

service.notify(
    "Welcome!",
    "alice@example.com"
)
```

---

## Output

```
Email sent to alice@example.com
```

---

## Enterprise Enhancements

```
NotificationController

↓

NotificationService

↓

TemplateService

↓

RetryService

↓

RateLimiter

↓

Message Queue

↓

NotificationChannel

↓

AuditRepository

↓

Database
```

---

## Design Patterns Used

- Strategy Pattern → Select notification channel at runtime
- Factory Pattern → Create channel instances
- Dependency Injection → Inject channel implementations
- Observer Pattern → Publish notification events
- Adapter Pattern → Integrate third-party providers
- Repository Pattern → Persist notification history

---

## SOLID Principles Applied

- **SRP:** Separate sending, templating, retries, and persistence.
- **OCP:** Add new channels by implementing `NotificationChannel`.
- **LSP:** All channels are interchangeable.
- **ISP:** Small interface with only `send()`.
- **DIP:** `NotificationService` depends on the `NotificationChannel` abstraction.

---

## Scalability Considerations

- Use asynchronous message queues (Kafka, RabbitMQ, SQS).
- Implement retry and dead-letter queues.
- Add rate limiting for external providers.
- Store delivery status and audit logs.
- Support idempotency to avoid duplicate notifications.
- Add metrics and monitoring for delivery success and failures.

---

## Interview Answer

> I would design the notification system using a layered architecture with a `NotificationService` depending on a `NotificationChannel` interface. Each channel (Email, SMS, Push, Slack, Teams) implements the same interface, allowing new channels to be added without modifying existing logic. Dependency injection, the Strategy pattern, and asynchronous messaging ensure the system is scalable, maintainable, and easy to test.

---

# Module Summary

- Favor composition over inheritance to achieve loose coupling, flexibility, and easier testing.
- Multiple inheritance is powerful but can introduce ambiguity; Python resolves it using the C3 Method Resolution Order (MRO).
- `@property` is built on the descriptor protocol (`__get__`, `__set__`, `__delete__`).
- Attribute lookup follows a well-defined order: `__getattribute__()` → data descriptors → instance dictionary → class attributes → MRO → `__getattr__()`.
- Object creation proceeds through `type.__call__()` → `__new__()` → `__init__()`.
- Python objects store metadata such as reference count, type pointer, and instance data; `__slots__` can optimize memory usage.
- Method binding is implemented through the descriptor protocol, automatically binding `self` to instance methods.
- Dynamic dispatch enables runtime polymorphism by selecting the appropriate method implementation based on the object's actual type.
- A scalable notification system should use interfaces, dependency injection, asynchronous messaging, and design patterns such as Strategy, Factory, Adapter, Observer, and Repository while following SOLID principles.