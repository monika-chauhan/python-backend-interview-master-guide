# Python OOP Interview Guide (Module 4)

# Inheritance

---

# 31. What is Inheritance?

## Definition

**Inheritance** is an Object-Oriented Programming (OOP) mechanism that allows a new class (called the **child** or **derived** class) to inherit attributes and methods from an existing class (called the **parent** or **base** class).

Instead of rewriting common code, the child class **reuses** and **extends** the parent class.

> **Inheritance represents an "is-a" relationship.**

Examples:

- Dog **is an** Animal
- Car **is a** Vehicle
- Manager **is an** Employee

---

## Basic Syntax

```python
class Parent:
    pass


class Child(Parent):
    pass
```

The child class automatically inherits the parent's public methods and attributes.

---

## Example

```python
class Animal:

    def eat(self):
        print("Eating...")


class Dog(Animal):

    def bark(self):
        print("Barking...")


dog = Dog()

dog.eat()
dog.bark()
```

Output

```
Eating...
Barking...
```

---

## Diagram

```
        Animal
           │
           │
      ┌────┴────┐
      │         │
    Dog       Cat
```

---

## Interview Answer

> Inheritance is an OOP feature that allows one class to inherit the properties and behavior of another class. It promotes code reuse, extensibility, and hierarchical relationships between classes.

---

# 32. Why is Inheritance Useful?

Without inheritance, similar code must be duplicated across multiple classes.

---

## Without Inheritance

```python
class Dog:

    def eat(self):
        print("Eating...")


class Cat:

    def eat(self):
        print("Eating...")


class Lion:

    def eat(self):
        print("Eating...")
```

The `eat()` method is duplicated.

---

## With Inheritance

```python
class Animal:

    def eat(self):
        print("Eating...")


class Dog(Animal):
    pass


class Cat(Animal):
    pass


class Lion(Animal):
    pass
```

Now all subclasses reuse the same implementation.

---

## Enterprise Example

```
Employee
    │
    ├── Developer
    ├── Tester
    ├── Manager
    └── HR
```

Common features:

- Employee ID
- Salary
- Attendance

Unique features:

- Developer → Write Code
- Tester → Test Software
- Manager → Approve Leave

---

## Benefits

- Code reuse
- Easier maintenance
- Reduced duplication
- Better organization
- Extensibility
- Supports polymorphism

---

## Interview Answer

> Inheritance is useful because it eliminates duplicate code, improves maintainability, supports extensibility, and models real-world hierarchical relationships effectively.

---

# 33. Types of Inheritance in Python

Python supports five major inheritance types:

1. Single Inheritance
2. Multiple Inheritance
3. Multilevel Inheritance
4. Hierarchical Inheritance
5. Hybrid Inheritance

---

## Overview Diagram

```
               Parent
                  │
      ┌───────────┴───────────┐
      │                       │
   Single              Hierarchical

Parent ──► Child

Multiple

 Parent1      Parent2
      \        /
       \      /
        Child

Multilevel

GrandParent
      │
 Parent
      │
 Child

Hybrid

Combination of Multiple Types
```

---

# 34. Single Inheritance

## Definition

A child class inherits from **one parent class**.

---

## Diagram

```
Parent
   │
   ▼
Child
```

---

## Example

```python
class Vehicle:

    def start(self):
        print("Vehicle Started")


class Car(Vehicle):

    def drive(self):
        print("Driving...")


car = Car()

car.start()
car.drive()
```

Output

```
Vehicle Started
Driving...
```

---

## Use Cases

- Employee → Developer
- Animal → Dog
- Shape → Circle

---

## Interview Answer

> Single inheritance occurs when a child class inherits from exactly one parent class. It is the simplest and most common form of inheritance.

---

# 35. Multiple Inheritance

## Definition

A class inherits from **more than one parent class**.

---

## Diagram

```
 Parent1      Parent2
      \        /
       \      /
        Child
```

---

## Example

```python
class Camera:

    def click(self):
        print("Photo Taken")


class Phone:

    def call(self):
        print("Calling...")


class SmartPhone(Camera, Phone):
    pass


mobile = SmartPhone()

mobile.click()
mobile.call()
```

Output

```
Photo Taken
Calling...
```

---

## Real-World Example

A smartphone combines:

- Camera
- Phone
- Music Player
- Internet Browser

---

## Diamond Problem

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

Python resolves this using the **Method Resolution Order (MRO)**, which follows the C3 linearization algorithm.

You can inspect it using:

```python
print(D.mro())
```

---

## Interview Answer

> Multiple inheritance allows a class to inherit from more than one parent class. Python resolves method conflicts using the Method Resolution Order (MRO).

---

# 36. Multilevel Inheritance

## Definition

Inheritance across multiple generations.

---

## Diagram

```
Grandparent
      │
      ▼
 Parent
      │
      ▼
 Child
```

---

## Example

```python
class Animal:

    def eat(self):
        print("Eating")


class Mammal(Animal):

    def walk(self):
        print("Walking")


class Dog(Mammal):

    def bark(self):
        print("Barking")


dog = Dog()

dog.eat()
dog.walk()
dog.bark()
```

Output

```
Eating
Walking
Barking
```

---

## Real-World Example

```
Vehicle

↓

Car

↓

Electric Car
```

---

## Interview Answer

> Multilevel inheritance occurs when a class inherits from another derived class, forming a chain of inheritance across multiple levels.

---

# 37. Hierarchical Inheritance

## Definition

Multiple child classes inherit from the same parent class.

---

## Diagram

```
          Animal
        /    |    \
      Dog   Cat   Lion
```

---

## Example

```python
class Animal:

    def eat(self):
        print("Eating")


class Dog(Animal):
    pass


class Cat(Animal):
    pass


dog = Dog()
cat = Cat()

dog.eat()
cat.eat()
```

Output

```
Eating
Eating
```

---

## Real-World Example

```
Employee

├── Developer

├── Tester

├── Manager

└── HR
```

All inherit common employee behavior.

---

## Interview Answer

> Hierarchical inheritance occurs when multiple child classes inherit from the same parent class, allowing them to share common functionality.

---

# 38. Hybrid Inheritance

## Definition

Hybrid inheritance is a combination of two or more inheritance types.

Examples include:

- Single + Multiple
- Hierarchical + Multiple
- Multilevel + Multiple

---

## Diagram

```
        A
       / \
      B   C
       \ /
        D
```

This combines hierarchical and multiple inheritance.

---

## Example

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


obj = D()

obj.show()
```

Output

```
A
```

Python uses MRO to resolve method lookup.

---

## Interview Answer

> Hybrid inheritance combines two or more inheritance types. Python supports hybrid inheritance and resolves method conflicts using the Method Resolution Order (MRO).

---

# 39. Advantages of Inheritance

## 1. Code Reusability

Write common logic once and reuse it across subclasses.

---

## 2. Reduced Code Duplication

Avoid repeating the same methods in multiple classes.

---

## 3. Better Maintainability

Changes in the parent class automatically propagate to child classes (unless overridden).

---

## 4. Extensibility

Add new functionality in child classes without modifying existing code.

---

## 5. Supports Polymorphism

A parent reference can work with different child objects.

Example:

```python
animals = [Dog(), Cat()]

for animal in animals:
    animal.speak()
```

---

## 6. Better Organization

Represents real-world hierarchies naturally.

---

## Enterprise Example

```
Employee

↓

Developer

↓

Senior Developer

↓

Tech Lead
```

Shared logic remains centralized.

---

## Interview Answer

> Inheritance improves code reuse, reduces duplication, enhances maintainability, supports polymorphism, and enables natural modeling of hierarchical relationships.

---

# 40. Disadvantages of Inheritance

Although powerful, inheritance should be used carefully.

---

## 1. Tight Coupling

Changes in the parent class may unintentionally affect all child classes.

---

## 2. Fragile Base Class Problem

A modification in the base class can break derived classes.

---

## 3. Deep Hierarchies Become Hard to Understand

Example:

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

Long inheritance chains reduce readability.

---

## 4. Multiple Inheritance Complexity

Method conflicts can occur when multiple parents define the same method.

Python resolves these conflicts using MRO, but understanding the resolution order can be challenging.

---

## 5. Incorrect Modeling

Using inheritance for "has-a" relationships is a design mistake.

Example:

```
Car is an Engine ❌
```

Correct:

```
Car has an Engine ✅
```

Composition is a better choice for "has-a" relationships.

---

## 6. Reduced Flexibility

A child class is tightly tied to its parent, making future changes more difficult.

---

## Best Practice

Prefer **composition over inheritance** when there is a "has-a" relationship.

Example:

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

Here, a `Car` **has an** `Engine` instead of inheriting from it.

---

## Interview Answer

> The main disadvantages of inheritance are tight coupling, deep inheritance hierarchies, complexity in multiple inheritance, and the risk of incorrect object modeling. For "has-a" relationships, composition is generally preferred over inheritance.

---

# Module Summary

- Inheritance allows a child class to reuse and extend the functionality of a parent class.
- It represents an **"is-a"** relationship.
- Python supports five types of inheritance:
  - Single
  - Multiple
  - Multilevel
  - Hierarchical
  - Hybrid
- Multiple inheritance is resolved using Python's **Method Resolution Order (MRO)** based on the C3 linearization algorithm.
- Advantages include code reuse, maintainability, extensibility, and polymorphism.
- Disadvantages include tight coupling, fragile base classes, deep hierarchies, and increased complexity.
- Use **composition** instead of inheritance when modeling **"has-a"** relationships.