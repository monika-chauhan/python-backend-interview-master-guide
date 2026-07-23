# Python OOP Interview Guide (Module 6)

# Composition vs Inheritance

---

# 61. What is Composition?

## Definition

**Composition** is an OOP design principle where one class **contains** another class as one of its attributes instead of inheriting from it.

It represents a **HAS-A** relationship.

Instead of extending another class, an object delegates work to the objects it owns.

> **Composition = Building complex objects by combining simpler objects.**

---

## Basic Example

```python
class Engine:

    def start(self):
        print("Engine Started")


class Car:

    def __init__(self):
        self.engine = Engine()

    def start(self):
        self.engine.start()


car = Car()
car.start()
```

Output

```
Engine Started
```

Here,

```
Car HAS-A Engine
```

---

## Diagram

```
Car
 │
 │ HAS-A
 ▼
Engine
```

---

## Interview Answer

> Composition is an OOP technique where one object contains another object to reuse functionality. It represents a HAS-A relationship and promotes loose coupling and flexibility.

---

# 62. Composition vs Inheritance

Inheritance and Composition both promote code reuse, but they model different relationships.

---

## Inheritance

Represents:

> **IS-A Relationship**

Example

```
Dog IS-A Animal
```

```python
class Animal:
    pass


class Dog(Animal):
    pass
```

---

## Composition

Represents:

> **HAS-A Relationship**

Example

```
Car HAS-A Engine
```

```python
class Engine:
    pass


class Car:

    def __init__(self):
        self.engine = Engine()
```

---

## Comparison Table

| Inheritance | Composition |
|-------------|-------------|
| IS-A relationship | HAS-A relationship |
| Tight coupling | Loose coupling |
| Uses `extends`/inheritance | Uses object references |
| Reuses implementation through subclassing | Reuses implementation through delegation |
| Harder to modify hierarchy | Easy to replace components |
| Can lead to deep hierarchies | Encourages modular design |

---

## Interview Answer

> Inheritance models an IS-A relationship, whereas composition models a HAS-A relationship. Composition is generally preferred because it produces loosely coupled, flexible, and more maintainable systems.

---

# 63. HAS-A vs IS-A Relationship

## IS-A

Represents inheritance.

Examples

```
Dog IS-A Animal

Manager IS-An Employee

Circle IS-A Shape
```

Python

```python
class Animal:
    pass


class Dog(Animal):
    pass
```

---

## HAS-A

Represents composition.

Examples

```
Car HAS-A Engine

Laptop HAS-A Keyboard

House HAS-A Room
```

Python

```python
class Engine:
    pass


class Car:

    def __init__(self):
        self.engine = Engine()
```

---

## Diagram

```
IS-A

Animal
   │
 Dog

-------------------

HAS-A

Car
 │
 ▼
Engine
```

---

## Rule of Thumb

Ask:

> **Can I truthfully say "is-a"?**

If yes → Inheritance

If no but I can say "has-a" → Composition

---

## Interview Answer

> IS-A relationships should be modeled using inheritance, while HAS-A relationships should be modeled using composition.

---

# 64. Why is Composition Preferred?

Modern software design strongly favors composition over inheritance.

---

## Reason 1: Loose Coupling

Objects depend on interfaces rather than parent classes.

---

## Reason 2: Easy Replacement

```python
car.engine = ElectricEngine()
```

No inheritance changes required.

---

## Reason 3: Better Testing

Dependencies can easily be mocked.

```python
car.engine = MockEngine()
```

---

## Reason 4: Avoids Deep Hierarchies

Instead of

```
Vehicle

↓

Car

↓

SportsCar

↓

ElectricSportsCar

↓

LuxuryElectricSportsCar
```

Use independent reusable components.

---

## Reason 5: Better Maintainability

Each class has a single responsibility.

---

## Interview Answer

> Composition is preferred because it reduces coupling, improves flexibility, simplifies testing, avoids deep inheritance hierarchies, and follows modern design principles such as "favor composition over inheritance."

---

# 65. When Should Inheritance Be Avoided?

Inheritance is useful only when there is a genuine **IS-A** relationship.

Avoid inheritance when:

---

## 1. Relationship is HAS-A

Bad

```python
class Car(Engine):
    pass
```

A Car is **not** an Engine.

Correct

```python
class Car:

    def __init__(self):
        self.engine = Engine()
```

---

## 2. Deep Class Hierarchies

```
Animal

↓

Mammal

↓

Carnivore

↓

Dog

↓

PoliceDog

↓

BombSquadDog
```

Hard to maintain.

---

## 3. Frequent Requirement Changes

Changing the base class may impact all subclasses.

---

## 4. Code Reuse Only

Never inherit just to reuse a few methods.

Use composition instead.

---

## Interview Answer

> Avoid inheritance when there is no true IS-A relationship, when class hierarchies become deep, or when code reuse is the only motivation. Composition is usually a better alternative.

---

# 66. Real-World Example Using Composition

## Banking System

```python
class NotificationService:

    def send(self, message):
        print(f"Notification: {message}")


class BankAccount:

    def __init__(self):
        self.notification = NotificationService()

    def deposit(self, amount):
        print(f"Deposited {amount}")
        self.notification.send("Deposit Successful")


account = BankAccount()

account.deposit(1000)
```

Output

```
Deposited 1000
Notification: Deposit Successful
```

---

## Diagram

```
BankAccount

 │ HAS-A

 ▼

NotificationService
```

The account delegates notification logic to another object.

---

## Interview Answer

> In a banking application, a `BankAccount` can use a `NotificationService` through composition instead of inheriting from it. Each class focuses on a single responsibility.

---

# 67. Dependency Injection Using Composition

Dependency Injection (DI) is a design pattern where dependencies are **provided from outside** instead of being created inside a class.

---

## Without Dependency Injection

```python
class UserService:

    def __init__(self):
        self.repo = UserRepository()
```

The service is tightly coupled to a specific repository.

---

## With Dependency Injection

```python
class UserRepository:

    def get_user(self):
        return "Alice"


class UserService:

    def __init__(self, repo):
        self.repo = repo

    def fetch(self):
        return self.repo.get_user()


repo = UserRepository()

service = UserService(repo)

print(service.fetch())
```

Output

```
Alice
```

---

## Benefits

- Loose coupling
- Easier testing
- Easier replacement of implementations
- Better maintainability

---

## Interview Answer

> Dependency Injection is commonly implemented using composition. Dependencies are supplied to a class rather than created internally, making the system more modular and testable.

---

# 68. Composition in Django

Django encourages composition over inheritance in many areas.

---

## Service Example

```python
class EmailService:

    def send(self, email):
        print("Sending Email")


class UserService:

    def __init__(self):
        self.email_service = EmailService()

    def register(self, email):
        # Save user
        self.email_service.send(email)
```

---

## Repository Pattern

```
View

↓

Service

↓

Repository

↓

Database
```

Each layer composes the next one.

---

## Benefits

- Separation of concerns
- Easier testing
- Cleaner architecture

---

## Interview Answer

> Django applications commonly use composition by separating business logic into services, repositories, serializers, and utility classes instead of placing everything in models or views.

---

# 69. Composition in FastAPI

FastAPI heavily relies on composition and dependency injection.

---

## Example

```python
class UserRepository:

    def get_user(self):
        return {"name": "Alice"}


class UserService:

    def __init__(self, repo):
        self.repo = repo

    def get_user(self):
        return self.repo.get_user()


repo = UserRepository()

service = UserService(repo)

print(service.get_user())
```

Output

```
{'name': 'Alice'}
```

---

## FastAPI Dependency Injection

```python
from fastapi import Depends

def get_service():
    return UserService(UserRepository())


@app.get("/users")
def get_users(service: UserService = Depends(get_service)):
    return service.get_user()
```

FastAPI injects the dependency automatically.

---

## Interview Answer

> FastAPI promotes composition through dependency injection. Services depend on repositories and are injected using `Depends`, resulting in loosely coupled and testable applications.

---

# 70. Service Layer Using Composition

A common enterprise architecture is:

```
API Layer

↓

Service Layer

↓

Repository Layer

↓

Database
```

---

## Example

```python
class UserRepository:

    def save(self):
        print("Saved")


class UserService:

    def __init__(self, repository):
        self.repository = repository

    def register(self):
        self.repository.save()


repo = UserRepository()

service = UserService(repo)

service.register()
```

Output

```
Saved
```

The service **has a** repository and delegates persistence to it.

---

## Benefits

- Clean architecture
- Separation of concerns
- Easy unit testing
- Reusable business logic

---

## Interview Answer

> In enterprise applications, the service layer composes repositories and other services instead of inheriting from them. This keeps business logic isolated and easy to test.

---

# 71. Enterprise Examples

## Example 1: E-commerce

```
OrderService

HAS-A

InventoryService

PaymentService

NotificationService

ShippingService
```

---

## Example 2: Banking

```
BankAccount

HAS-A

FraudService

NotificationService

AuditService

TransactionService
```

---

## Example 3: Healthcare

```
PatientService

HAS-A

AppointmentService

BillingService

InsuranceService

NotificationService
```

---

## Example 4: Microservices

```
AuthService

HAS-A

JWTService

CacheService

DatabaseRepository

Logger
```

---

## Example 5: Django Backend

```
View

↓

UserService

↓

UserRepository

↓

PostgreSQL
```

---

## Example 6: FastAPI Backend

```
Route

↓

Service

↓

Repository

↓

SQLAlchemy

↓

Database
```

---

## Why Enterprises Prefer Composition

- Independent components
- Easy mocking in tests
- Better scalability
- Easier maintenance
- Supports Dependency Injection
- Follows SOLID principles (especially Single Responsibility and Dependency Inversion)

---

## Interview Answer

> Enterprise applications favor composition by building systems from small, focused components such as services, repositories, loggers, notification services, and payment providers. This approach improves modularity, scalability, and testability.

---

# Module Summary

- Composition models a **HAS-A** relationship, while inheritance models an **IS-A** relationship.
- Composition reuses functionality by containing objects instead of extending classes.
- Modern software design generally favors composition because it reduces coupling and improves flexibility.
- Use inheritance only for genuine IS-A relationships.
- Dependency Injection is commonly implemented through composition.
- Django and FastAPI architectures rely heavily on composition by separating concerns into services, repositories, and utilities.
- Enterprise applications compose services such as payment, notification, logging, caching, and repositories to build scalable and maintainable systems.
- **Rule of thumb:** *Favor composition over inheritance unless inheritance clearly models the domain.*