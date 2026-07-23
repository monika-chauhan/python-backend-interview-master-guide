# Python OOP Interview Guide (Module 9)

# OOP in Backend Systems

---

# 91. How Would You Design a Scalable User Management System Using OOP?

## Goal

Design a system that is:

- Scalable
- Maintainable
- Testable
- Loosely Coupled
- Easy to Extend

Instead of placing all logic inside a single class, responsibilities should be separated into layers.

---

## High-Level Architecture

```
                Client
                   │
                   ▼
          REST API / FastAPI / Django
                   │
                   ▼
           Controller / View Layer
                   │
                   ▼
              Service Layer
                   │
         ┌─────────┴─────────┐
         ▼                   ▼
 Repository Layer     Notification Service
         │                   │
         ▼                   ▼
      Database         Email / SMS
```

---

## Class Responsibilities

### Entity

```python
class User:

    def __init__(self, id, name, email):
        self.id = id
        self.name = name
        self.email = email
```

---

### Repository

```python
class UserRepository:

    def get_by_email(self, email):
        pass

    def save(self, user):
        pass
```

---

### Service

```python
class UserService:

    def __init__(self, repository):
        self.repository = repository

    def register(self, user):
        self.repository.save(user)
```

---

### Notification Service

```python
class EmailService:

    def send(self, email):
        print("Email Sent")
```

---

## SOLID Principles Used

- SRP → Separate Repository, Service, Notification
- DIP → Inject Repository into Service
- OCP → Easily add new notification providers
- ISP → Small interfaces
- LSP → Swappable repository implementations

---

## Interview Answer

> I would design a scalable user management system using layered architecture with entities, repositories, services, and controllers. Each layer has a single responsibility, dependencies are injected through abstractions, and business logic remains isolated from persistence and presentation.

---

# 92. How Do You Apply OOP in Django Models?

Django models are Python classes and naturally leverage OOP concepts.

---

## Example

```python
from django.db import models


class User(models.Model):

    name = models.CharField(max_length=100)
    email = models.EmailField()

    def full_name(self):
        return self.name.upper()
```

---

## Better Enterprise Design

Avoid placing complex business logic directly in models.

```
Views

↓

Services

↓

Repositories

↓

Models
```

---

### Service Example

```python
class UserService:

    def register(self, data):
        user = User.objects.create(**data)
        return user
```

---

## OOP Concepts Used

- Encapsulation
- Inheritance (`models.Model`)
- Abstraction
- Composition
- Polymorphism (custom managers, serializers)

---

## Interview Answer

> Django models apply OOP through inheritance from `models.Model`, encapsulation of data and behavior, and reusable methods. In enterprise applications, business logic is typically moved to services, while models focus on representing data.

---

# 93. How Do You Apply OOP in FastAPI Services?

FastAPI encourages a layered architecture with dependency injection.

---

## Architecture

```
Route

↓

Service

↓

Repository

↓

Database
```

---

## Repository

```python
class UserRepository:

    def get(self, user_id):
        return {"id": user_id, "name": "Alice"}
```

---

## Service

```python
class UserService:

    def __init__(self, repository):
        self.repository = repository

    def get_user(self, user_id):
        return self.repository.get(user_id)
```

---

## Route

```python
from fastapi import Depends, FastAPI

app = FastAPI()


def get_service():
    return UserService(UserRepository())


@app.get("/users/{user_id}")
def get_user(user_id: int, service: UserService = Depends(get_service)):
    return service.get_user(user_id)
```

---

## Benefits

- Dependency Injection
- Loose Coupling
- Testability
- Reusable Services

---

## Interview Answer

> In FastAPI, OOP is applied by separating routes, services, and repositories. Dependencies are injected using `Depends`, enabling modular, testable, and maintainable applications.

---

# 94. How Do You Structure a Scalable Backend Using OOP?

A common enterprise folder structure is:

```
project/

├── api/
│   ├── routes/
│   └── controllers/
│
├── services/
│
├── repositories/
│
├── models/
│
├── schemas/
│
├── database/
│
├── utils/
│
├── middleware/
│
└── tests/
```

---

## Layered Flow

```
API

↓

Controller

↓

Service

↓

Repository

↓

Database
```

---

## Advantages

- Clear separation of concerns
- Easy scalability
- Reusable business logic
- Independent testing

---

## Interview Answer

> A scalable backend is structured using layered architecture. Controllers handle requests, services implement business logic, repositories manage persistence, and models represent domain entities. This improves modularity and maintainability.

---

# 95. How Do You Avoid God Classes?

## What is a God Class?

A **God Class** is a class that performs too many unrelated responsibilities.

Example:

```python
class UserManager:

    def login(self):
        pass

    def register(self):
        pass

    def send_email(self):
        pass

    def generate_report(self):
        pass

    def export_csv(self):
        pass

    def process_payment(self):
        pass
```

This violates the Single Responsibility Principle.

---

## Better Design

```
AuthenticationService

EmailService

PaymentService

ReportService

UserService
```

Each class focuses on one responsibility.

---

## Tips

- Apply SRP
- Keep classes small
- Use composition over inheritance
- Extract reusable services
- Avoid large utility classes

---

## Interview Answer

> God classes are avoided by applying the Single Responsibility Principle, extracting responsibilities into dedicated services, and keeping classes focused on one business concern.

---

# 96. How Do You Design Reusable Services?

Reusable services should:

- Have a single responsibility
- Be stateless where possible
- Depend on abstractions
- Be framework-independent
- Receive dependencies through injection

---

## Example

```python
class EmailService:

    def send(self, email, subject, body):
        print("Email Sent")
```

The same service can be reused by:

- User registration
- Password reset
- Order confirmation
- Invoice generation

---

## Interview Answer

> Reusable services are designed around a single business capability, avoid framework-specific code, depend on abstractions, and receive dependencies through dependency injection.

---

# 97. Entity vs DTO vs Service vs Repository

These are common layers in enterprise applications.

---

## Entity

Represents domain data.

```python
class User:
    pass
```

---

## DTO (Data Transfer Object)

Transfers data between layers.

```python
class UserDTO:

    def __init__(self, name, email):
        self.name = name
        self.email = email
```

---

## Service

Contains business logic.

```python
class UserService:

    def register(self):
        pass
```

---

## Repository

Handles database operations.

```python
class UserRepository:

    def save(self):
        pass
```

---

## Responsibilities

| Layer | Responsibility |
|--------|----------------|
| Entity | Domain object |
| DTO | Transfer data between layers |
| Service | Business logic |
| Repository | Database access |

---

## Interview Answer

> Entities model domain data, DTOs transfer data between layers, services implement business rules, and repositories encapsulate data access. Separating these concerns results in a clean and maintainable architecture.

---

# 98. Domain-Driven Design (DDD) and OOP

## What is DDD?

Domain-Driven Design is an architectural approach where software is modeled around the business domain.

OOP concepts map naturally to DDD.

---

## Building Blocks

```
Entity

↓

Value Object

↓

Repository

↓

Service

↓

Aggregate

↓

Domain Events
```

---

## Example

```
Banking

Customer

Account

Transaction

Loan
```

Each is modeled as a domain entity with behavior.

---

## Benefits

- Business-focused design
- High maintainability
- Rich domain models
- Clear boundaries

---

## Interview Answer

> Domain-Driven Design uses object-oriented principles to model real-world business concepts. Entities, value objects, repositories, and domain services work together to represent business rules and behavior.

---

# 99. OOP in Microservices

Each microservice should model a single business capability using OOP internally.

---

## Example

```
User Service

↓

UserService

↓

UserRepository

↓

User Entity

↓

Database
```

---

## Payment Service

```
PaymentController

↓

PaymentService

↓

PaymentRepository

↓

StripeAdapter

↓

Database
```

---

## OOP Concepts

- Encapsulation
- Abstraction
- Composition
- Dependency Injection
- Strategy Pattern
- Factory Pattern

---

## Benefits

- Independent deployment
- Reusable components
- Easier testing
- Clear ownership

---

## Interview Answer

> Within a microservice, OOP organizes business logic into entities, services, repositories, and adapters. Each microservice owns a single business capability and communicates with others through APIs or messaging.

---

# 100. OOP Best Practices for Enterprise Applications

## 1. Follow SOLID Principles

Keep classes focused, loosely coupled, and open for extension.

---

## 2. Prefer Composition Over Inheritance

Use inheritance only for true **IS-A** relationships.

---

## 3. Keep Classes Small

One responsibility per class.

---

## 4. Use Dependency Injection

Inject repositories, services, and clients rather than creating them inside classes.

---

## 5. Separate Layers

```
Controller

↓

Service

↓

Repository

↓

Database
```

---

## 6. Avoid Business Logic in Controllers

Controllers should validate requests and delegate work to services.

---

## 7. Avoid Fat Models

Keep Django models focused on persistence and simple domain behavior.

---

## 8. Use Interfaces or Abstract Base Classes

Program against abstractions instead of concrete implementations.

---

## 9. Write Unit Tests

Mock repositories and external services to test business logic in isolation.

---

## 10. Follow Clean Architecture

```
Presentation

↓

Application

↓

Domain

↓

Infrastructure
```

Dependencies should point inward toward the domain.

---

## Enterprise Checklist

- ✅ SOLID principles
- ✅ Composition over inheritance
- ✅ Dependency Injection
- ✅ Repository Pattern
- ✅ Service Layer
- ✅ DTOs for data transfer
- ✅ Stateless services where appropriate
- ✅ Small, cohesive classes
- ✅ Automated tests
- ✅ Clear separation of concerns

---

## Interview Answer

> Enterprise OOP emphasizes SOLID principles, layered architecture, dependency injection, composition, and separation of concerns. Business logic belongs in services, persistence in repositories, and domain behavior in entities, resulting in scalable, maintainable, and testable applications.

---

# Module Summary

- Scalable backend systems are built using layered architecture with **Controllers → Services → Repositories → Database**.
- Django applies OOP through models, services, repositories, and class-based views, while FastAPI leverages dependency injection and service classes.
- Avoid God classes by applying the Single Responsibility Principle and decomposing functionality into focused services.
- Reusable services should be stateless where possible, framework-independent, and depend on abstractions.
- **Entities** represent domain objects, **DTOs** transfer data, **Services** contain business logic, and **Repositories** manage persistence.
- Domain-Driven Design (DDD) models business concepts using OOP constructs such as entities, value objects, repositories, and domain services.
- In microservices, each service encapsulates a single business capability and internally follows OOP principles for modularity and maintainability.
- Enterprise best practices include SOLID, composition over inheritance, dependency injection, clean architecture, and comprehensive testing.