# Python OOP Interview Guide (Module 1)

# Design Patterns Using OOP

---

# 1. What are Design Patterns?

## Definition

**Design Patterns** are **proven, reusable solutions** to common software design problems.

They are **templates**, not ready-made code.

Design patterns help developers build software that is:

- Reusable
- Scalable
- Maintainable
- Flexible
- Loosely Coupled

---

## Categories of Design Patterns

The Gang of Four (GoF) classifies design patterns into three categories:

### 1. Creational Patterns

Focus on object creation.

Examples:

- Singleton
- Factory
- Builder
- Prototype

---

### 2. Structural Patterns

Focus on class and object composition.

Examples:

- Adapter
- Decorator
- Facade
- Composite

---

### 3. Behavioral Patterns

Focus on communication between objects.

Examples:

- Strategy
- Observer
- Command
- State

---

## Enterprise Examples

| Pattern | Real-world Usage |
|----------|-----------------|
| Singleton | Database Connection Pool |
| Factory | Payment Gateway |
| Builder | HTTP Request Builder |
| Prototype | Object Cloning |
| Adapter | Third-party API Integration |
| Decorator | Authentication Middleware |
| Strategy | Payment Algorithms |
| Observer | Notifications |
| Repository | Database Access Layer |

---

## Interview Answer

> Design patterns are reusable solutions to recurring software design problems. They improve maintainability, scalability, and code reuse by providing well-established design approaches.

---

# 2. Singleton Pattern

## Definition

The Singleton Pattern ensures that:

- Only **one instance** of a class exists.
- A global access point is provided.

---

## Use Cases

- Logger
- Database Connection
- Configuration Manager
- Cache Manager
- Thread Pool

---

## Example

```python
class Singleton:

    _instance = None

    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance


a = Singleton()
b = Singleton()

print(a is b)
```

Output

```
True
```

---

## Diagram

```
Application

     |

     ▼

 Singleton

     |

     ▼

 One Instance
```

---

## Advantages

- Saves memory
- Centralized state
- Easy global access

---

## Disadvantages

- Global state
- Harder testing
- Can introduce hidden dependencies

---

## Interview Answer

> The Singleton Pattern ensures that only one instance of a class exists and provides a global access point. It is commonly used for loggers, configuration managers, and database connection pools.

---

# 3. Factory Pattern

## Definition

The Factory Pattern creates objects **without exposing object creation logic** to the client.

Instead of using `if-else` blocks throughout the application, object creation is centralized.

---

## Example

```python
from abc import ABC, abstractmethod


class Payment(ABC):

    @abstractmethod
    def pay(self):
        pass


class CreditCard(Payment):

    def pay(self):
        print("Credit Card Payment")


class UPI(Payment):

    def pay(self):
        print("UPI Payment")


class PaymentFactory:

    @staticmethod
    def create(method):

        if method == "card":
            return CreditCard()

        if method == "upi":
            return UPI()

        raise ValueError("Unsupported payment method")


payment = PaymentFactory.create("upi")
payment.pay()
```

Output

```
UPI Payment
```

---

## Benefits

- Centralized object creation
- Open for extension
- Reduces coupling

---

## Enterprise Usage

- Database drivers
- Payment gateways
- Cloud providers
- Notification services

---

## Interview Answer

> The Factory Pattern encapsulates object creation logic in a dedicated factory class. It reduces coupling and makes it easy to introduce new object types without affecting client code.

---

# 4. Builder Pattern

## Definition

The Builder Pattern constructs **complex objects step by step**.

Useful when an object has many optional fields.

---

## Example

```python
class User:

    def __init__(self):
        self.name = None
        self.age = None
        self.city = None


class UserBuilder:

    def __init__(self):
        self.user = User()

    def set_name(self, name):
        self.user.name = name
        return self

    def set_age(self, age):
        self.user.age = age
        return self

    def set_city(self, city):
        self.user.city = city
        return self

    def build(self):
        return self.user


user = (
    UserBuilder()
    .set_name("Alice")
    .set_age(25)
    .set_city("Delhi")
    .build()
)

print(user.name)
```

---

## Real-world Examples

- HTTP Request Builders
- SQL Query Builders
- Docker Image Builders
- Cloud SDKs

---

## Interview Answer

> The Builder Pattern constructs complex objects incrementally. It improves readability and is useful when objects have many optional or configurable fields.

---

# 5. Prototype Pattern

## Definition

The Prototype Pattern creates new objects by **cloning existing objects** instead of constructing them from scratch.

---

## Example

```python
import copy


class Employee:

    def __init__(self, name):
        self.name = name


emp1 = Employee("Alice")

emp2 = copy.deepcopy(emp1)

print(emp1.name)
print(emp2.name)
```

Output

```
Alice
Alice
```

---

## Use Cases

- Game development
- Machine learning models
- Configuration templates
- Expensive object creation

---

## Interview Answer

> The Prototype Pattern creates new objects by copying existing ones. It is useful when object creation is expensive or when many similar objects are required.

---

# 6. Adapter Pattern

## Definition

The Adapter Pattern allows **incompatible interfaces** to work together.

It acts as a translator between two classes.

---

## Example

```python
class LegacyAPI:

    def fetch_data(self):
        return "Legacy Data"


class Adapter:

    def __init__(self, api):
        self.api = api

    def get(self):
        return self.api.fetch_data()


adapter = Adapter(LegacyAPI())

print(adapter.get())
```

Output

```
Legacy Data
```

---

## Real-world Examples

- Third-party APIs
- Database drivers
- Payment gateways
- Cloud SDKs

---

## Interview Answer

> The Adapter Pattern converts one interface into another expected by the client, allowing incompatible classes or systems to work together without modifying their code.

---

# 7. Decorator Pattern

## Definition

The Decorator Pattern adds **new behavior dynamically** without modifying the original class.

---

## Example

```python
class Coffee:

    def cost(self):
        return 100


class MilkDecorator:

    def __init__(self, coffee):
        self.coffee = coffee

    def cost(self):
        return self.coffee.cost() + 20


coffee = MilkDecorator(Coffee())

print(coffee.cost())
```

Output

```
120
```

---

## Python Example

Function decorators:

```python
@login_required
def profile():
    pass
```

---

## Django Examples

- `@login_required`
- `@permission_required`
- Middleware

---

## Interview Answer

> The Decorator Pattern dynamically extends an object's behavior without modifying its original implementation. Python decorators are a common example of this pattern.

---

# 8. Strategy Pattern

## Definition

The Strategy Pattern encapsulates multiple algorithms and allows them to be selected at runtime.

---

## Example

```python
from abc import ABC, abstractmethod


class PaymentStrategy(ABC):

    @abstractmethod
    def pay(self, amount):
        pass


class CreditCardStrategy(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid {amount} using Card")


class UPIStrategy(PaymentStrategy):

    def pay(self, amount):
        print(f"Paid {amount} using UPI")


class PaymentService:

    def __init__(self, strategy):
        self.strategy = strategy

    def checkout(self, amount):
        self.strategy.pay(amount)


service = PaymentService(UPIStrategy())
service.checkout(500)
```

Output

```
Paid 500 using UPI
```

---

## Enterprise Examples

- Payment gateways
- Tax calculation
- Shipping algorithms
- Authentication providers

---

## Interview Answer

> The Strategy Pattern encapsulates interchangeable algorithms behind a common interface, allowing behavior to be selected dynamically at runtime.

---

# 9. Observer Pattern

## Definition

The Observer Pattern defines a **one-to-many relationship**.

When one object changes, all dependent objects are notified automatically.

---

## Example

```python
class Subscriber:

    def update(self, message):
        print(message)


class Publisher:

    def __init__(self):
        self.subscribers = []

    def subscribe(self, subscriber):
        self.subscribers.append(subscriber)

    def notify(self, message):
        for subscriber in self.subscribers:
            subscriber.update(message)


publisher = Publisher()

publisher.subscribe(Subscriber())

publisher.notify("New Product Available")
```

Output

```
New Product Available
```

---

## Real-world Examples

- Notifications
- Event systems
- WebSockets
- Django Signals

---

## Interview Answer

> The Observer Pattern allows multiple objects to be notified automatically when another object's state changes. It is commonly used in event-driven systems and notification services.

---

# 10. Dependency Injection Pattern

## Definition

Dependency Injection (DI) provides an object's dependencies **from outside** rather than creating them internally.

This reduces coupling and improves testability.

---

## Bad Example

```python
class UserService:

    def __init__(self):
        self.repo = UserRepository()
```

---

## Good Example

```python
class UserRepository:

    def get_user(self):
        return "Alice"


class UserService:

    def __init__(self, repo):
        self.repo = repo

    def fetch_user(self):
        return self.repo.get_user()


repo = UserRepository()

service = UserService(repo)

print(service.fetch_user())
```

Output

```
Alice
```

---

## Benefits

- Loose coupling
- Easier testing
- Better maintainability
- Swappable implementations

---

## FastAPI Example

```python
from fastapi import Depends

def get_service():
    return UserService(UserRepository())
```

---

## Interview Answer

> Dependency Injection supplies dependencies from outside the class instead of creating them internally. This promotes loose coupling, testability, and flexibility.

---

# 11. Repository Pattern

## Definition

The Repository Pattern abstracts **data access logic** from business logic.

Instead of querying the database directly inside services, services communicate with repositories.

---

## Architecture

```
API

↓

Service

↓

Repository

↓

Database
```

---

## Example

```python
class UserRepository:

    def get_user(self, user_id):
        return {"id": user_id, "name": "Alice"}


class UserService:

    def __init__(self, repository):
        self.repository = repository

    def fetch_user(self, user_id):
        return self.repository.get_user(user_id)


repo = UserRepository()

service = UserService(repo)

print(service.fetch_user(1))
```

Output

```
{'id': 1, 'name': 'Alice'}
```

---

## Benefits

- Separation of concerns
- Easy database replacement
- Better testing
- Cleaner architecture

---

## Django Example

```python
class UserRepository:

    def get_by_id(self, user_id):
        return User.objects.get(id=user_id)
```

---

## Enterprise Architecture

```
View

↓

Service

↓

Repository

↓

ORM

↓

Database
```

---

## Interview Answer

> The Repository Pattern separates business logic from data access logic. Services interact with repositories instead of the database directly, improving maintainability, testability, and flexibility.

---

# Module Summary

- Design patterns are reusable solutions to common software design problems.
- **Singleton** ensures only one instance of a class exists.
- **Factory** centralizes object creation and reduces coupling.
- **Builder** constructs complex objects step by step.
- **Prototype** creates objects by cloning existing ones.
- **Adapter** enables incompatible interfaces to work together.
- **Decorator** adds behavior dynamically without modifying existing code.
- **Strategy** encapsulates interchangeable algorithms and selects them at runtime.
- **Observer** notifies dependent objects when state changes.
- **Dependency Injection** provides dependencies externally, promoting loose coupling.
- **Repository** separates business logic from data access logic.
- These patterns are widely used in enterprise frameworks such as **Django**, **FastAPI**, **Spring Boot**, and cloud-native microservices.