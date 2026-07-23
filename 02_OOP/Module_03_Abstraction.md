# Python OOP Interview Guide (Module 3)

# Abstraction

---

# 21. What is Abstraction?

## Definition

**Abstraction** is the process of **hiding implementation details** and exposing **only the essential features** of an object.

In simple words:

> **Focus on "what an object does" instead of "how it does it."**

Users interact with a simple interface without knowing the complex logic behind it.

Abstraction is one of the **four pillars of Object-Oriented Programming (OOP)**.

---

## Real-World Example

### Driving a Car

When driving a car, you only use:

- Steering Wheel
- Brake
- Accelerator
- Gear

You do **not** need to know:

- Fuel injection system
- Engine timing
- Gearbox internals
- ECU programming

```
Driver
   │
   ▼
Steering / Brake / Accelerator
   │
   ▼
Complex Engine Logic (Hidden)
```

This is abstraction.

---

## Python Example

```python
from abc import ABC, abstractmethod

class Animal(ABC):

    @abstractmethod
    def speak(self):
        pass


class Dog(Animal):

    def speak(self):
        return "Bark"


dog = Dog()

print(dog.speak())
```

Output

```
Bark
```

The user only knows that every animal can `speak()`. Each subclass decides **how**.

---

## Benefits

- Reduces complexity
- Hides implementation details
- Improves maintainability
- Promotes loose coupling
- Makes APIs easier to understand

---

## Interview Answer

> Abstraction is the process of hiding implementation details while exposing only the necessary functionality. It simplifies complex systems by allowing users to interact with objects through well-defined interfaces.

---

# 22. Why Do We Need Abstraction?

Imagine an application like Amazon.

Users only care about:

```
Place Order

↓

Pay

↓

Track Order
```

They do not care about:

- Inventory allocation
- Payment gateway integration
- Database transactions
- Logistics algorithms

---

## Without Abstraction

```
Customer

↓

Inventory Logic

↓

Shipping Logic

↓

Payment Logic

↓

Database Logic

↓

Notification Logic
```

Everything is exposed.

Very complex.

---

## With Abstraction

```
Customer

↓

place_order()

↓

System Handles Everything
```

Simple interface.

---

## Benefits in Large Systems

- Easier to use
- Reduced complexity
- Independent implementation changes
- Better scalability
- Cleaner APIs
- Improved maintainability

---

## Interview Answer

> Abstraction is needed to simplify complex systems by exposing only the required functionality. It reduces coupling, hides implementation details, and allows internal changes without affecting users of the API.

---

# 23. Difference Between Abstraction and Encapsulation

These concepts are often confused.

---

## Encapsulation

Focuses on:

> **Protecting data**

It binds data and methods together and controls access to internal state.

---

## Abstraction

Focuses on:

> **Hiding complexity**

It exposes only essential functionality.

---

## Example

### ATM Machine

Encapsulation

```
Balance

↓

Hidden

↓

Deposit()

Withdraw()
```

Protects the balance.

---

Abstraction

```
ATM Screen

↓

Withdraw Money

↓

Hidden Banking Logic
```

Hides complexity.

---

## Comparison Table

| Encapsulation | Abstraction |
|--------------|-------------|
| Hides data | Hides implementation |
| Achieved using access control | Achieved using abstract classes/interfaces |
| Focuses on security | Focuses on simplicity |
| Restricts direct access | Exposes only required functionality |

---

## Interview Answer

> Encapsulation protects an object's internal state by restricting direct access, whereas abstraction hides implementation details and exposes only the necessary functionality. Encapsulation focuses on data protection, while abstraction focuses on reducing complexity.

---

# 24. What is an Abstract Base Class (ABC)?

## Definition

An **Abstract Base Class (ABC)** is a class that:

- Cannot be instantiated directly
- Defines one or more abstract methods
- Serves as a blueprint for subclasses

Python provides the `abc` module for creating abstract classes.

---

## Example

```python
from abc import ABC, abstractmethod

class Shape(ABC):

    @abstractmethod
    def area(self):
        pass
```

`Shape` defines the contract.

Every subclass must implement `area()`.

---

## Concrete Class

```python
class Circle(Shape):

    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14 * self.radius ** 2
```

---

## Interview Answer

> An Abstract Base Class is a class that cannot be instantiated and is intended to define a common interface for its subclasses. It enforces implementation of required methods using the `abc` module.

---

# 25. How Does Python Implement Abstraction?

Python provides the built-in `abc` module.

Important components:

- `ABC`
- `@abstractmethod`

---

## Example

```python
from abc import ABC, abstractmethod

class Vehicle(ABC):

    @abstractmethod
    def start(self):
        pass
```

Concrete implementation

```python
class Car(Vehicle):

    def start(self):
        print("Car Started")
```

---

## Internally

```
ABC

↓

Contains Abstract Methods

↓

Subclass Inherits

↓

Must Implement Methods

↓

Otherwise Instantiation Fails
```

---

## Interview Answer

> Python implements abstraction using the `abc` module. Classes inherit from `ABC`, and methods decorated with `@abstractmethod` must be implemented by concrete subclasses before they can be instantiated.

---

# 26. Can an Abstract Class Have Concrete Methods?

## Yes.

An abstract class may contain:

- Abstract methods
- Normal (concrete) methods
- Class methods
- Static methods
- Properties

---

## Example

```python
from abc import ABC, abstractmethod

class Animal(ABC):

    @abstractmethod
    def speak(self):
        pass

    def sleep(self):
        print("Sleeping...")
```

Concrete class

```python
class Dog(Animal):

    def speak(self):
        print("Bark")


dog = Dog()

dog.sleep()
```

Output

```
Sleeping...
```

---

## Why?

Concrete methods allow sharing common functionality across subclasses while still enforcing specific behaviors through abstract methods.

---

## Interview Answer

> Yes. An abstract class can contain both abstract and concrete methods. Concrete methods provide shared functionality, while abstract methods define behavior that subclasses must implement.

---

# 27. Can an Abstract Class Have Constructors?

## Yes.

Abstract classes can define constructors (`__init__()`).

---

## Example

```python
from abc import ABC, abstractmethod

class Animal(ABC):

    def __init__(self, name):
        self.name = name

    @abstractmethod
    def speak(self):
        pass


class Dog(Animal):

    def speak(self):
        print("Bark")


dog = Dog("Rocky")

print(dog.name)
```

Output

```
Rocky
```

---

## Why?

Constructors initialize common state shared by all subclasses.

---

## Interview Answer

> Yes. Abstract classes can define constructors to initialize common attributes that are shared by all subclasses.

---

# 28. Can We Instantiate an Abstract Class?

## No.

Attempting to instantiate an abstract class raises a `TypeError`.

---

## Example

```python
from abc import ABC, abstractmethod

class Shape(ABC):

    @abstractmethod
    def area(self):
        pass


shape = Shape()
```

Output

```text
TypeError:
Can't instantiate abstract class Shape
with abstract method area
```

---

## Correct Usage

```python
class Circle(Shape):

    def area(self):
        return 100


circle = Circle()

print(circle.area())
```

Output

```
100
```

---

## Interview Answer

> No. An abstract class cannot be instantiated directly. Only concrete subclasses that implement all abstract methods can be instantiated.

---

# 29. Interface vs Abstract Class

Python does not have a dedicated `interface` keyword like Java.

Developers often use Abstract Base Classes to achieve similar behavior.

---

## Interface (Concept)

- Defines a contract
- Usually contains only abstract methods
- No instance state (in many languages)

---

## Abstract Class

- Can contain abstract methods
- Can contain concrete methods
- Can define constructors
- Can maintain shared state

---

## Comparison

| Interface | Abstract Class |
|-----------|----------------|
| Defines behavior contract | Defines contract + shared implementation |
| Usually no state | Can have state |
| Usually only abstract methods | Can mix abstract and concrete methods |
| Multiple interfaces are common | Used for shared base functionality |

---

## Python Perspective

Since Python is flexible, an Abstract Base Class often plays the role of both an interface and an abstract class.

---

## Interview Answer

> An interface defines a contract for behavior, while an abstract class can define both a contract and shared implementation. In Python, Abstract Base Classes are commonly used to achieve interface-like behavior.

---

# 30. Real-World Examples of Abstraction

## Example 1: Car

Driver uses:

```
start()

stop()

accelerate()
```

Hidden:

- Engine ignition
- Fuel injection
- Transmission
- ECU logic

---

## Example 2: ATM

Customer sees:

```
Withdraw

Deposit

Balance Inquiry
```

Hidden:

- Authentication
- Database access
- Transaction logging
- Encryption

---

## Example 3: Payment Gateway

Application code:

```python
payment.process()
```

Hidden:

- Tokenization
- Fraud detection
- Bank communication
- Currency conversion
- Retries

---

## Example 4: Cloud Storage (AWS S3 / Google Cloud Storage)

Developer writes:

```python
storage.upload(file)
```

Hidden:

- Replication
- Encryption
- Networking
- Distributed storage
- Retry mechanisms

---

## Example 5: Database ORM (Django)

Developer writes:

```python
User.objects.get(id=1)
```

Hidden:

- SQL generation
- Connection pooling
- Query optimization
- Result mapping

---

## Enterprise Example

```
Developer

↓

API

↓

Business Layer

↓

Database

↓

Storage

↓

Infrastructure
```

The developer only interacts with the API, while the implementation details remain hidden.

---

## Interview Answer

> Real-world examples of abstraction include driving a car, using an ATM, processing online payments, uploading files to cloud storage, and interacting with databases through ORMs. In each case, users work with a simple interface while the underlying complexity remains hidden.

---

# Module Summary

- Abstraction hides implementation details while exposing essential functionality.
- It simplifies complex systems and promotes loose coupling.
- Python implements abstraction using the `abc` module with `ABC` and `@abstractmethod`.
- Abstract Base Classes define contracts that subclasses must implement.
- Abstract classes can contain constructors, concrete methods, class methods, static methods, and properties.
- Abstract classes cannot be instantiated directly.
- In Python, Abstract Base Classes often serve the role of interfaces.
- Real-world abstractions include cars, ATMs, payment gateways, cloud storage, and ORMs like Django.