# Python OOP Interview Guide (Module 1)

# SOLID Principles

---

# 1. What is SOLID?

## Definition

**SOLID** is a set of **five object-oriented design principles** introduced by **Robert C. Martin (Uncle Bob)**.

These principles help developers build software that is:

- Maintainable
- Scalable
- Flexible
- Testable
- Loosely Coupled

Instead of focusing only on writing working code, SOLID focuses on writing **clean, extensible, and maintainable code**.

---

## SOLID Stands For

| Letter | Principle |
|----------|------------|
| **S** | Single Responsibility Principle (SRP) |
| **O** | Open Closed Principle (OCP) |
| **L** | Liskov Substitution Principle (LSP) |
| **I** | Interface Segregation Principle (ISP) |
| **D** | Dependency Inversion Principle (DIP) |

---

## Why SOLID?

Without SOLID:

- Code becomes tightly coupled.
- Small changes break unrelated features.
- Unit testing becomes difficult.
- Adding new functionality requires modifying existing code.
- Large projects become difficult to maintain.

With SOLID:

- High cohesion
- Low coupling
- Better code reuse
- Easier testing
- Easier feature additions
- Cleaner architecture

---

## Real-World Example

Imagine an **E-commerce Application**.

```
Order Service

↓

Payment Service

↓

Inventory Service

↓

Notification Service
```

Each service has a single responsibility and interacts through well-defined interfaces.

---

## Interview Answer

> SOLID is a collection of five object-oriented design principles that help developers create maintainable, scalable, loosely coupled, and testable software. These principles form the foundation of clean architecture and enterprise software design.

---

# 2. Single Responsibility Principle (SRP)

## Definition

> **A class should have only one reason to change.**

A class should perform **one well-defined responsibility**.

---

## Bad Example

```python
class User:

    def save(self):
        print("Saving user")

    def send_email(self):
        print("Sending email")

    def generate_report(self):
        print("Generating report")
```

The class is responsible for:

- Persistence
- Notifications
- Reporting

Too many responsibilities.

---

## Good Example

```python
class UserRepository:

    def save(self):
        print("Saving user")


class EmailService:

    def send_email(self):
        print("Sending email")


class ReportService:

    def generate_report(self):
        print("Generating report")
```

Each class has one responsibility.

---

## Benefits

- Easier maintenance
- Better testing
- Lower coupling
- Higher cohesion

---

## Interview Answer

> The Single Responsibility Principle states that a class should have only one reason to change. Each class should focus on a single responsibility or business concern.

---

# 3. Open Closed Principle (OCP)

## Definition

> **Software entities should be open for extension but closed for modification.**

You should be able to add new functionality **without changing existing code**.

---

## Bad Example

```python
class Payment:

    def pay(self, method):

        if method == "card":
            print("Card Payment")

        elif method == "upi":
            print("UPI Payment")
```

Adding a new payment type requires modifying the class.

---

## Good Example

```python
from abc import ABC, abstractmethod

class Payment(ABC):

    @abstractmethod
    def pay(self):
        pass


class CreditCardPayment(Payment):

    def pay(self):
        print("Card Payment")


class UPIPayment(Payment):

    def pay(self):
        print("UPI Payment")
```

Adding another payment method:

```python
class WalletPayment(Payment):

    def pay(self):
        print("Wallet Payment")
```

No existing code changes.

---

## Benefits

- Easy extension
- Reduced bugs
- Stable codebase
- Better scalability

---

## Interview Answer

> The Open Closed Principle states that classes should be open for extension but closed for modification. New behavior should be added by extending existing code rather than modifying it.

---

# 4. Liskov Substitution Principle (LSP)

## Definition

> **Objects of a superclass should be replaceable with objects of its subclasses without breaking the program.**

A subclass must honor the behavior expected from its parent.

---

## Correct Example

```python
class Bird:

    def make_sound(self):
        print("Bird Sound")


class Sparrow(Bird):

    def make_sound(self):
        print("Chirp")
```

Anywhere a `Bird` is expected, a `Sparrow` can be used safely.

---

## Bad Example

```python
class Bird:

    def fly(self):
        print("Flying")


class Penguin(Bird):

    def fly(self):
        raise NotImplementedError("Penguins cannot fly")
```

The subclass violates the parent's contract.

---

## Better Design

```python
class Bird:
    pass


class FlyingBird(Bird):

    def fly(self):
        print("Flying")


class Sparrow(FlyingBird):
    pass


class Penguin(Bird):
    pass
```

---

## Interview Answer

> The Liskov Substitution Principle states that subclasses should be substitutable for their parent classes without changing the correctness of the program. A subclass should not violate the expectations established by the base class.

---

# 5. Interface Segregation Principle (ISP)

## Definition

> **Clients should not be forced to depend on methods they do not use.**

Instead of one large interface, create multiple smaller, focused interfaces.

---

## Bad Example

```python
from abc import ABC, abstractmethod

class Worker(ABC):

    @abstractmethod
    def work(self):
        pass

    @abstractmethod
    def eat(self):
        pass
```

A robot worker may not need `eat()`.

---

## Good Example

```python
class Workable(ABC):

    @abstractmethod
    def work(self):
        pass


class Eatable(ABC):

    @abstractmethod
    def eat(self):
        pass


class Human(Workable, Eatable):

    def work(self):
        print("Working")

    def eat(self):
        print("Eating")


class Robot(Workable):

    def work(self):
        print("Working")
```

---

## Benefits

- Smaller interfaces
- Better flexibility
- Easier implementation
- Cleaner design

---

## Interview Answer

> The Interface Segregation Principle states that clients should not be forced to implement or depend on methods they do not require. It encourages creating smaller, more focused interfaces.

---

# 6. Dependency Inversion Principle (DIP)

## Definition

> **High-level modules should not depend on low-level modules. Both should depend on abstractions.**

Also:

> **Abstractions should not depend on details. Details should depend on abstractions.**

---

## Bad Example

```python
class MySQLDatabase:

    def save(self):
        print("Saved")


class UserService:

    def __init__(self):
        self.db = MySQLDatabase()
```

The service is tightly coupled to MySQL.

---

## Good Example

```python
from abc import ABC, abstractmethod

class Database(ABC):

    @abstractmethod
    def save(self):
        pass


class MySQLDatabase(Database):

    def save(self):
        print("Saved to MySQL")


class PostgreSQLDatabase(Database):

    def save(self):
        print("Saved to PostgreSQL")


class UserService:

    def __init__(self, database):
        self.database = database

    def register(self):
        self.database.save()
```

Usage

```python
db = PostgreSQLDatabase()

service = UserService(db)

service.register()
```

---

## Benefits

- Loose coupling
- Easy testing
- Easier dependency replacement
- Better scalability

---

## Interview Answer

> The Dependency Inversion Principle states that high-level modules should depend on abstractions rather than concrete implementations. This reduces coupling and improves flexibility and testability.

---

# 7. Violating SRP Example

## Bad Design

```python
class Employee:

    def calculate_salary(self):
        pass

    def save_to_database(self):
        pass

    def send_email(self):
        pass

    def generate_payslip(self):
        pass
```

Responsibilities:

- Salary calculation
- Database operations
- Email notifications
- Reporting

Multiple reasons to change.

---

## Better Design

```python
class SalaryCalculator:
    pass


class EmployeeRepository:
    pass


class EmailService:
    pass


class PayslipService:
    pass
```

Each class has one responsibility.

---

## Interview Answer

> A class that calculates salaries, stores data, sends emails, and generates reports violates SRP because it has multiple reasons to change. These responsibilities should be separated into dedicated classes.

---

# 8. Violating LSP Example

## Bad Example

```python
class Rectangle:

    def set_width(self, width):
        self.width = width

    def set_height(self, height):
        self.height = height


class Square(Rectangle):

    def set_width(self, width):
        self.width = self.height = width

    def set_height(self, height):
        self.width = self.height = height
```

Client code expecting independent width and height fails when given a `Square`.

---

## Better Design

Create separate abstractions instead of forcing a square to behave like a rectangle.

---

## Another Example

```python
class Bird:

    def fly(self):
        pass


class Penguin(Bird):

    def fly(self):
        raise Exception("Cannot fly")
```

The subclass breaks the contract.

---

## Interview Answer

> LSP is violated when a subclass cannot fully replace its parent without changing program behavior. Examples include `Square` inheriting from `Rectangle` with incompatible behavior or `Penguin` inheriting from a flying `Bird`.

---

# 9. Applying SOLID in Django

A common Django architecture applies SOLID by separating responsibilities into layers.

```
View

↓

Serializer

↓

Service

↓

Repository

↓

Model

↓

Database
```

---

## Example

### Repository

```python
class UserRepository:

    def get_user(self, user_id):
        return User.objects.get(id=user_id)
```

### Service

```python
class UserService:

    def __init__(self, repository):
        self.repository = repository

    def fetch_user(self, user_id):
        return self.repository.get_user(user_id)
```

### View

```python
def user_detail(request, user_id):

    service = UserService(UserRepository())

    user = service.fetch_user(user_id)
```

---

## SOLID Mapping

| Principle | Django Example |
|------------|----------------|
| SRP | Separate View, Service, Repository |
| OCP | New payment providers through subclasses |
| LSP | Swappable repository implementations |
| ISP | Small service interfaces |
| DIP | Inject repositories into services |

---

## Benefits

- Thin views
- Reusable business logic
- Easy unit testing
- Better scalability
- Cleaner architecture

---

## Interview Answer

> In Django, SOLID is applied by separating responsibilities into views, serializers, services, repositories, and models. Dependency injection and abstraction make the application modular, testable, and easy to maintain.

---

# 10. Applying SOLID in Microservices

Microservices naturally encourage SOLID principles.

---

## Architecture

```
API Gateway

        │

 ┌──────┼─────────┐

 │      │         │

User  Order   Payment

Service Service Service

 │       │        │

Repository Repository Repository

 │       │        │

Database Database Database
```

---

## SRP

Each microservice owns a single business capability.

Examples:

- User Service
- Payment Service
- Inventory Service
- Notification Service

---

## OCP

Support new payment providers by implementing a common interface.

---

## LSP

Different repository implementations (PostgreSQL, MongoDB, Redis) should be interchangeable.

---

## ISP

Each service exposes only the APIs required by its consumers.

---

## DIP

Services depend on interfaces rather than concrete implementations.

Example:

```
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

## Enterprise Example

```
Order Service

HAS-A

Inventory Service

Payment Provider

Notification Service

Audit Service

Logger
```

Each dependency is injected through abstractions, making the system modular and testable.

---

## Interview Answer

> SOLID principles are widely applied in microservices by giving each service a single responsibility, using abstractions for integrations, injecting dependencies, exposing focused APIs, and designing interchangeable implementations for external systems.

---

# Module Summary

- SOLID is a collection of five design principles for building maintainable and scalable software.
- **SRP:** A class should have only one responsibility.
- **OCP:** Software should be open for extension but closed for modification.
- **LSP:** Subclasses must be substitutable for their parent classes.
- **ISP:** Prefer small, focused interfaces over large ones.
- **DIP:** Depend on abstractions instead of concrete implementations.
- Violating SRP leads to classes with multiple reasons to change.
- Violating LSP breaks polymorphism and client expectations.
- Django applies SOLID using layered architecture (Views, Services, Repositories, Models).
- Microservices naturally align with SOLID by separating business capabilities and depending on abstractions.
- SOLID principles improve code quality, scalability, maintainability, and testability in enterprise applications.