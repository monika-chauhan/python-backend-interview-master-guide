# Python OOP Interview Guide (Module 10)

# Production & System Design

---

# 131. When Should You Avoid Inheritance?

## Definition

Inheritance is a powerful feature of OOP, but it should be used **only when there is a true "IS-A" relationship**.

Overusing inheritance creates:

- Tight coupling
- Fragile hierarchies
- Difficult maintenance
- Reduced flexibility

Modern software design follows the principle:

> **"Favor Composition over Inheritance."**

---

## When to Avoid Inheritance

### 1. When There is No IS-A Relationship

❌ Bad Example

```python
class Engine:
    pass


class Car(Engine):
    pass
```

A Car is **not** an Engine.

✔ Better

```python
class Engine:
    pass


class Car:

    def __init__(self):
        self.engine = Engine()
```

---

### 2. Deep Class Hierarchies

❌

```
Vehicle

↓

Car

↓

ElectricCar

↓

LuxuryElectricCar

↓

SportsLuxuryElectricCar
```

Hard to maintain.

✔ Use composition.

---

### 3. Code Reuse Only

Never inherit simply to reuse methods.

Instead:

```
Service

HAS-A

Logger
```

---

### 4. Frequently Changing Requirements

If subclasses constantly override parent behavior, inheritance is likely the wrong abstraction.

---

## Interview Answer

> Avoid inheritance when there is no genuine IS-A relationship, when hierarchies become deep, or when the goal is only code reuse. In such cases, composition provides better flexibility and maintainability.

---

# 132. How Does OOP Improve Testability?

Good OOP promotes **small, loosely coupled classes**, making unit testing easier.

---

## Without OOP

```python
def register_user(data):
    save_to_db(data)
    send_email(data)
    publish_event(data)
```

Difficult to test because everything is tightly coupled.

---

## With OOP

```python
class UserService:

    def __init__(self, repository, email_service):
        self.repository = repository
        self.email_service = email_service

    def register(self, user):
        self.repository.save(user)
        self.email_service.send(user.email)
```

Now dependencies can be mocked.

---

## Unit Test

```python
mock_repo = MockRepository()

mock_email = MockEmailService()

service = UserService(mock_repo, mock_email)
```

---

## Benefits

- Mock dependencies
- Independent testing
- Faster execution
- Better isolation

---

## Interview Answer

> OOP improves testability by separating responsibilities and using dependency injection. Services depend on abstractions, allowing repositories and external integrations to be mocked during unit testing.

---

# 133. How Does Dependency Injection Improve Scalability?

Dependency Injection (DI) supplies dependencies from outside instead of creating them internally.

---

## Without DI

```python
class UserService:

    def __init__(self):
        self.repo = PostgreSQLRepository()
```

Changing the database requires modifying the class.

---

## With DI

```python
class UserService:

    def __init__(self, repository):
        self.repository = repository
```

Now any repository can be injected.

```python
UserService(PostgresRepository())

UserService(MongoRepository())

UserService(MockRepository())
```

---

## Benefits

- Loose coupling
- Easy scaling
- Replace implementations
- Easier testing
- Supports plugins

---

## Enterprise Example

```
PaymentService

↓

PaymentProvider Interface

↓

Stripe

↓

PayPal

↓

Razorpay
```

---

## Interview Answer

> Dependency injection improves scalability by reducing coupling between components. Implementations can be replaced without modifying business logic, making systems easier to extend and test.

---

# 134. OOP vs Functional Programming

## OOP

Focuses on **objects**.

Combines:

- Data
- Behavior

inside classes.

---

## Functional Programming

Focuses on:

- Pure functions
- Immutable data
- Function composition
- No shared state

---

## Example

### OOP

```python
class Calculator:

    def add(self, a, b):
        return a + b
```

---

### Functional

```python
def add(a, b):
    return a + b
```

---

## Comparison

| OOP | Functional |
|------|------------|
| Objects | Functions |
| Mutable state | Immutable state |
| Encapsulation | Pure functions |
| Inheritance | Function composition |
| Polymorphism | Higher-order functions |

---

## Which is Better?

Modern Python uses both.

Examples:

- Django → Mostly OOP
- FastAPI → Mix of OOP and Functional
- Pandas → OOP API with functional operations

---

## Interview Answer

> OOP organizes software around objects that combine data and behavior, while functional programming emphasizes pure functions and immutable data. Modern Python applications often combine both paradigms.

---

# 135. OOP in Asynchronous Applications

Asynchronous applications still benefit from OOP.

---

## Example

```python
class UserService:

    async def get_user(self, user_id):
        return {"id": user_id}
```

---

## FastAPI Example

```python
class EmailService:

    async def send(self):
        print("Email Sent")
```

---

## Service Layer

```
API

↓

UserService

↓

Repository

↓

Async Database
```

---

## Benefits

- Reusable async services
- Better organization
- Easier testing
- Separation of concerns

---

## Interview Answer

> OOP organizes asynchronous code into reusable services, repositories, and clients. Async methods (`async`/`await`) are added to classes without changing the overall architecture.

---

# 136. OOP in Event-Driven Architecture

In event-driven systems, components communicate through events instead of direct method calls.

---

## Example

```
Order Created

↓

Event Bus

↓

Inventory Service

↓

Notification Service

↓

Analytics Service
```

---

## Publisher

```python
class OrderService:

    def create_order(self):
        print("Publishing OrderCreated Event")
```

---

## Subscriber

```python
class NotificationService:

    def handle_order_created(self):
        print("Email Sent")
```

---

## Benefits

- Loose coupling
- Independent services
- Easy extensibility

---

## Enterprise Examples

- Kafka
- RabbitMQ
- AWS SNS/SQS
- Azure Service Bus

---

## Interview Answer

> OOP models publishers, subscribers, handlers, and event processors as separate classes. This promotes loose coupling and aligns well with event-driven architectures.

---

# 137. OOP in Distributed Systems

Distributed systems consist of multiple independent services communicating over a network.

Each service internally follows OOP principles.

---

## Architecture

```
API Gateway

      │

 ┌────┴────┐

 │         │

User   Payment

Service Service

 │         │

Repository Repository
```

---

## OOP Components

- Controllers
- Services
- Repositories
- Adapters
- Clients
- DTOs

---

## Benefits

- Modular services
- Independent deployment
- Easy testing
- Better maintainability

---

## Interview Answer

> In distributed systems, each service encapsulates its own domain using OOP. Services communicate through APIs or messaging while internally applying layered architecture, dependency injection, and design patterns.

---

# 138. OOP Pitfalls in Production

Common mistakes include:

---

## 1. God Classes

One class doing everything.

---

## 2. Deep Inheritance

Hard to understand.

---

## 3. Tight Coupling

Classes directly instantiate dependencies.

---

## 4. Excessive Abstraction

Too many interfaces for simple problems.

---

## 5. Fat Models

Business logic inside ORM models.

---

## 6. Circular Dependencies

```
Service A

↓

Service B

↓

Service A
```

---

## 7. Mutable Shared State

Causes race conditions.

---

## Best Practices

- Prefer composition
- Apply SOLID
- Use dependency injection
- Keep classes focused
- Write unit tests

---

## Interview Answer

> Common OOP pitfalls include God classes, deep inheritance hierarchies, tight coupling, excessive abstraction, and business logic scattered across inappropriate layers. Applying SOLID principles and composition helps avoid these issues.

---

# 139. Refactoring Procedural Code to OOP

## Procedural Version

```python
def save_user(user):
    print("Saved")


def send_email(user):
    print("Email Sent")


def register(user):
    save_user(user)
    send_email(user)
```

---

## OOP Version

```python
class UserRepository:

    def save(self, user):
        print("Saved")


class EmailService:

    def send(self, user):
        print("Email Sent")


class UserService:

    def __init__(self, repository, email_service):
        self.repository = repository
        self.email_service = email_service

    def register(self, user):
        self.repository.save(user)
        self.email_service.send(user)
```

---

## Advantages

- Easier testing
- Better organization
- Reusable components
- Loose coupling

---

## Interview Answer

> Refactoring procedural code to OOP involves identifying responsibilities, creating focused classes, injecting dependencies, and separating business logic from infrastructure concerns.

---

# 140. Designing a Payment Gateway Using OOP

## Requirements

Support multiple payment providers:

- Stripe
- Razorpay
- PayPal

The design should allow adding new providers without changing existing business logic.

---

## Architecture

```
Client

↓

PaymentService

↓

PaymentProvider Interface

↓

Stripe

↓

Razorpay

↓

PayPal
```

---

## Abstract Provider

```python
from abc import ABC, abstractmethod

class PaymentProvider(ABC):

    @abstractmethod
    def pay(self, amount):
        pass
```

---

## Providers

```python
class StripeProvider(PaymentProvider):

    def pay(self, amount):
        print(f"Paid {amount} using Stripe")


class RazorpayProvider(PaymentProvider):

    def pay(self, amount):
        print(f"Paid {amount} using Razorpay")
```

---

## Service

```python
class PaymentService:

    def __init__(self, provider):
        self.provider = provider

    def process(self, amount):
        self.provider.pay(amount)
```

---

## Usage

```python
service = PaymentService(StripeProvider())

service.process(1000)
```

Output

```
Paid 1000 using Stripe
```

---

## Design Patterns Used

- Strategy Pattern
- Dependency Injection
- Factory Pattern (optional provider creation)
- Repository Pattern (payment persistence)
- Adapter Pattern (third-party gateways)

---

## SOLID Principles Used

- SRP → Separate provider implementations
- OCP → Add new providers without changing `PaymentService`
- LSP → All providers implement the same interface
- ISP → Small payment interface
- DIP → Service depends on abstraction

---

## Enterprise Enhancements

```
PaymentController

↓

PaymentService

↓

PaymentProvider

↓

FraudService

↓

AuditService

↓

NotificationService

↓

PaymentRepository

↓

Database
```

---

## Interview Answer

> I would design a payment gateway using a layered architecture with a `PaymentService` depending on a `PaymentProvider` abstraction. Each provider (Stripe, Razorpay, PayPal) implements the same interface, allowing new gateways to be added without modifying business logic. Dependency injection, the Strategy pattern, and SOLID principles ensure the system is scalable, testable, and maintainable.

---

# Module Summary

- Use inheritance only for true **IS-A** relationships; prefer composition for flexibility.
- OOP improves testability through small, cohesive classes and dependency injection.
- Dependency injection enhances scalability by allowing implementations to be swapped without changing business logic.
- OOP organizes software around objects, while functional programming emphasizes pure functions and immutable data; Python often combines both.
- Asynchronous applications benefit from OOP by encapsulating async behavior in services and repositories.
- Event-driven architectures model publishers, subscribers, and handlers as separate classes.
- Distributed systems apply OOP within each service using layered architecture and design patterns.
- Avoid common production pitfalls such as God classes, deep inheritance, tight coupling, excessive abstraction, and circular dependencies.
- Refactoring procedural code to OOP improves modularity, maintainability, and testability.
- A scalable payment gateway can be designed using Strategy, Dependency Injection, Factory, Adapter, and Repository patterns while adhering to SOLID principles.