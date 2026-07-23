# Python OOP Interview Guide (Module 2)

# Encapsulation

---

# 11. What is Encapsulation?

## Definition

**Encapsulation** is the process of **binding data (attributes) and behavior (methods) together into a single unit (class)** while **restricting direct access to internal data**.

In simple words:

> Encapsulation hides an object's internal implementation and exposes only a controlled interface to interact with it.

It is one of the four fundamental pillars of Object-Oriented Programming (OOP).

---

## Real-World Example

Think about an **ATM Machine**.

You only interact with:

- Withdraw Money
- Deposit Money
- Check Balance

You **cannot directly modify** the bank's internal database.

```
Customer
    │
    ▼
ATM Interface
    │
    ▼
Bank Database (Hidden)
```

This is Encapsulation.

---

## Python Example

```python
class BankAccount:

    def __init__(self):
        self.__balance = 1000

    def deposit(self, amount):
        self.__balance += amount

    def get_balance(self):
        return self.__balance


account = BankAccount()

account.deposit(500)

print(account.get_balance())
```

Output

```
1500
```

The balance is hidden from direct access.

---

## Benefits

- Protects data
- Prevents accidental modification
- Improves maintainability
- Improves security
- Makes APIs easier to use

---

## Interview Answer

> Encapsulation is the process of combining data and methods into a single class while restricting direct access to an object's internal state. It protects data integrity by exposing only controlled interfaces.

---

# 12. Why is Encapsulation Important in Large Systems?

Large software systems may contain:

- Hundreds of developers
- Thousands of classes
- Millions of users

Without encapsulation, anyone can modify internal data.

Example:

```
Payment.balance

↓

Developer A changes directly

↓

Developer B changes directly

↓

System becomes inconsistent
```

---

## Banking Example

Bad Design

```python
account.balance = -50000
```

Anyone can make the balance negative.

Good Design

```python
account.withdraw(500)
```

Validation happens inside the method.

---

## Benefits in Enterprise Systems

- Data consistency
- Security
- Easier debugging
- Reduced coupling
- Easier maintenance
- Controlled business rules

---

## Amazon Example

Customer should never modify:

- Order Status
- Payment Status
- Inventory

Instead:

```
place_order()

cancel_order()

refund()

ship()
```

These methods enforce business rules.

---

## Interview Answer

> Encapsulation is important in large systems because it protects critical business data, reduces coupling, enforces business rules, and allows internal implementations to evolve without affecting external code.

---

# 13. How Does Python Implement Encapsulation?

Python follows the philosophy:

> **"We are all consenting adults here."**

Instead of strict access modifiers like Java (`private`, `protected`, `public`), Python uses naming conventions.

There are three common levels of access:

### Public

```python
class Student:
    def __init__(self):
        self.name = "Alice"
```

Accessible everywhere.

---

### Protected (Convention)

```python
class Student:
    def __init__(self):
        self._marks = 90
```

The single underscore signals:

> "This is intended for internal use."

Python does not prevent access.

---

### Private (Name Mangling)

```python
class Student:
    def __init__(self):
        self.__salary = 50000
```

Python rewrites the attribute name internally (name mangling), making accidental access less likely.

---

## Interview Answer

> Python implements encapsulation through naming conventions and name mangling. Public members are freely accessible, protected members use a single underscore by convention, and private members use double underscores, which trigger name mangling.

---

# 14. What is Name Mangling?

## Definition

Name mangling is Python's mechanism for reducing accidental access to attributes prefixed with **double underscores (`__`)**.

Python internally rewrites:

```
__balance
```

to:

```
_ClassName__balance
```

---

## Example

```python
class Bank:

    def __init__(self):
        self.__balance = 1000


bank = Bank()

print(bank._Bank__balance)
```

Output

```
1000
```

Internally, the instance dictionary contains:

```python
print(bank.__dict__)
```

Output

```python
{'_Bank__balance': 1000}
```

---

## Why Name Mangling?

Suppose:

```python
class Parent:
    def __init__(self):
        self.__id = 1

class Child(Parent):
    def __init__(self):
        super().__init__()
        self.__id = 2
```

Parent and child attributes do not collide because they are mangled to different names.

---

## Important Note

Name mangling **does not provide true security**.

It mainly prevents accidental access and attribute name clashes in subclasses.

---

## Interview Answer

> Name mangling is Python's automatic transformation of attributes starting with double underscores into `_ClassName__attribute`. It helps avoid accidental access and naming conflicts, especially in inheritance.

---

# 15. Difference Between `_variable` and `__variable`

| `_variable` | `__variable` |
|-------------|--------------|
| Protected by convention | Name-mangled by Python |
| Accessible directly | Access requires mangled name |
| Signals internal use | Reduces accidental access |
| Commonly used in APIs | Used for implementation details |

---

## Example

```python
class Demo:

    def __init__(self):
        self._x = 10
        self.__y = 20


obj = Demo()

print(obj._x)
```

Works.

```
10
```

```python
print(obj.__y)
```

Raises:

```
AttributeError
```

Correct access (if absolutely necessary):

```python
print(obj._Demo__y)
```

---

## Interview Answer

> A single underscore is a convention indicating an internal attribute, while a double underscore triggers name mangling to reduce accidental access and naming conflicts. Neither provides true access control.

---

# 16. Is Encapsulation Truly Enforced in Python?

## Short Answer

**No.**

Python relies on developer discipline rather than strict enforcement.

Example:

```python
class Demo:

    def __init__(self):
        self.__salary = 1000


d = Demo()

print(d._Demo__salary)
```

Output

```
1000
```

Although direct access via `d.__salary` fails, the mangled name is still accessible.

---

## Why?

Python emphasizes:

> "We are all consenting adults here."

The language trusts developers to respect conventions.

---

## Comparison

| Language | Private Access |
|-----------|----------------|
| Java | Strictly enforced |
| C++ | Strictly enforced |
| Python | Convention + name mangling |

---

## Interview Answer

> Encapsulation is not strictly enforced in Python. Instead, Python uses naming conventions and name mangling to discourage accidental access while trusting developers to use the API responsibly.

---

# 17. How Do Properties (`@property`) Implement Encapsulation?

Properties allow you to expose an attribute while controlling how it is accessed or modified.

---

## Example

```python
class Employee:

    def __init__(self):
        self._salary = 50000

    @property
    def salary(self):
        return self._salary

    @salary.setter
    def salary(self, value):
        if value < 0:
            raise ValueError("Salary cannot be negative")
        self._salary = value


emp = Employee()

emp.salary = 60000

print(emp.salary)
```

Output

```
60000
```

---

## Benefits

- Validation
- Computed attributes
- Lazy evaluation
- Maintain backward compatibility

Users interact with `salary` like an attribute, while validation happens behind the scenes.

---

## Interview Answer

> The `@property` decorator allows controlled access to internal attributes while preserving attribute-like syntax. It enables validation, computed values, and future implementation changes without altering the public API.

---

# 18. Difference Between Getters/Setters and Properties

## Traditional Getters and Setters

```python
class Employee:

    def get_salary(self):
        return self._salary

    def set_salary(self, value):
        self._salary = value
```

Usage

```python
emp.set_salary(50000)

print(emp.get_salary())
```

---

## Using Properties

```python
class Employee:

    @property
    def salary(self):
        return self._salary

    @salary.setter
    def salary(self, value):
        self._salary = value
```

Usage

```python
emp.salary = 50000

print(emp.salary)
```

---

## Comparison

| Getters/Setters | Properties |
|-----------------|------------|
| Method syntax | Attribute syntax |
| More verbose | Cleaner and Pythonic |
| Explicit calls | Transparent access |
| Common in Java | Preferred in Python |

---

## Interview Answer

> Getters and setters require explicit method calls, while properties provide the same control using normal attribute syntax. Properties are the Pythonic approach because they keep the API simple while preserving encapsulation.

---

# 19. Real-World Example of Encapsulation in Banking

```python
class BankAccount:

    def __init__(self, balance):
        self.__balance = balance

    @property
    def balance(self):
        return self.__balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Deposit amount must be positive")
        self.__balance += amount

    def withdraw(self, amount):
        if amount > self.__balance:
            raise ValueError("Insufficient funds")
        self.__balance -= amount


account = BankAccount(1000)

account.deposit(500)

account.withdraw(200)

print(account.balance)
```

Output

```
1300
```

---

## Why This is Good Design

The user cannot bypass business rules by directly modifying the balance.

All updates go through validated methods.

```
Customer
    │
    ▼
Deposit / Withdraw
    │
Validation
    │
    ▼
Private Balance
```

---

## Interview Answer

> In banking systems, sensitive data such as account balances should not be modified directly. Encapsulation ensures all changes occur through validated operations like `deposit()` and `withdraw()`, preserving data integrity and enforcing business rules.

---

# 20. When Should You Expose Internal State?

Expose internal state **only when it is safe and necessary**.

Good candidates:

- Read-only information
- Computed values
- Immutable data
- Configuration values

Avoid exposing:

- Passwords
- Authentication tokens
- Bank balances for modification
- Internal caches
- Database connections
- Security-sensitive objects

---

## Example

Good

```python
@property
def balance(self):
    return self.__balance
```

Bad

```python
account.balance = -100000
```

---

## Best Practices

- Prefer read-only properties for sensitive data.
- Validate all modifications.
- Keep implementation details private.
- Expose behavior (`deposit`, `withdraw`) instead of raw state.
- Design APIs around business actions rather than direct data manipulation.

---

## Interview Answer

> Internal state should be exposed only when required by the application's API and when doing so does not compromise data integrity or security. Prefer exposing behavior over mutable internal data, and use read-only properties or validated setters when access is necessary.

---

# Module Summary

- Encapsulation combines data and behavior while restricting direct access.
- Python implements encapsulation using naming conventions and name mangling.
- `__variable` becomes `_ClassName__variable` through name mangling.
- Encapsulation in Python is based on conventions, not strict enforcement.
- `@property` provides controlled attribute access with validation.
- Properties are more Pythonic than traditional getters and setters.
- Encapsulation protects business rules in real-world systems like banking.
- Expose internal state only when necessary, and always through a controlled interface.