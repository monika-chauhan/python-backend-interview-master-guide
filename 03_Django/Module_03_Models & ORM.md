# Django Interview Master Guide

# Module 3: Models & ORM (Questions 21–45)

---

# 21. What is ORM?

## Definition

**ORM (Object-Relational Mapping)** is a technique that maps Python objects to relational database tables.

Instead of writing SQL manually, developers interact with the database using Python classes and objects.

Django's ORM automatically translates Python code into SQL.

---

## Example

```python
class Employee(models.Model):
    name = models.CharField(max_length=100)
    salary = models.IntegerField()
```

Query:

```python
Employee.objects.filter(salary__gt=50000)
```

Generated SQL (simplified):

```sql
SELECT * FROM employee
WHERE salary > 50000;
```

---

## Advantages

- Database abstraction
- Cleaner code
- SQL injection protection
- Database portability
- Easier maintenance

---

## Interview Answer

> Django ORM is an Object-Relational Mapper that maps Python classes to database tables. It allows developers to query and manipulate data using Python objects while automatically generating SQL.

---

# 22. ORM vs Raw SQL

| Feature | ORM | Raw SQL |
|----------|-----|---------|
| Readability | High | Lower |
| Portability | High | Database-specific |
| SQL Injection Protection | Yes (parameterized) | Manual responsibility |
| Performance | Good | Can be optimized further |
| Complex Queries | Supported | Maximum flexibility |

---

## ORM Example

```python
Employee.objects.filter(age__gt=25)
```

---

## Raw SQL Example

```python
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute(
        "SELECT * FROM employee WHERE age > %s",
        [25],
    )
    rows = cursor.fetchall()
```

---

## When to Use Raw SQL

- Vendor-specific features
- Extremely complex queries
- Performance-critical cases
- Unsupported SQL constructs

---

## Interview Answer

> ORM improves productivity, readability, and portability, while raw SQL provides maximum control and can be preferable for highly optimized or database-specific queries.

---

# 23. Explain Django Model Lifecycle

## Lifecycle

```
Create Instance

↓

Validation (optional via full_clean)

↓

save()

↓

pre_save Signal

↓

Generate SQL

↓

Database

↓

post_save Signal
```

---

## Example

```python
employee = Employee(name="Alice")

employee.save()
```

---

## Internally

1. Create Python object.
2. Prepare field values.
3. Execute `pre_save`.
4. Generate SQL.
5. Execute INSERT/UPDATE.
6. Execute `post_save`.

---

## Interview Answer

> A Django model lifecycle includes object creation, optional validation, `save()`, execution of `pre_save` signals, SQL generation, database persistence, and `post_save` signals.

---

# 24. Model Inheritance Types

Django supports three inheritance models.

1. Abstract Base Class
2. Multi-table Inheritance
3. Proxy Models

---

## Comparison

| Type | Separate Table | Purpose |
|------|----------------|---------|
| Abstract | No | Reuse fields |
| Multi-table | Yes | Extend data model |
| Proxy | No | Change Python behavior |

---

## Interview Answer

> Django provides abstract base classes for shared fields, multi-table inheritance for extending database schemas, and proxy models for changing Python behavior without altering the database structure.

---

# 25. Abstract Base Class

## Purpose

Reuse common fields without creating a database table.

---

## Example

```python
class TimeStamp(models.Model):
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        abstract = True


class Employee(TimeStamp):
    name = models.CharField(max_length=100)
```

---

## Database

Only the `Employee` table is created.

---

## Use Cases

- Audit fields
- Soft delete fields
- Common metadata

---

## Interview Answer

> An abstract base class shares fields and methods among multiple models without creating its own database table.

---

# 26. Multi-table Inheritance

Creates one table per model.

---

## Example

```python
class Person(models.Model):
    name = models.CharField(max_length=100)


class Employee(Person):
    salary = models.IntegerField()
```

---

## Database

```
Person Table

Employee Table
```

Linked by an implicit `OneToOneField`.

---

## Interview Answer

> Multi-table inheritance creates a separate table for each model and links child tables to parent tables through an implicit one-to-one relationship.

---

# 27. Proxy Models

A proxy model changes Python behavior without creating a new table.

---

## Example

```python
class Employee(models.Model):
    name = models.CharField(max_length=100)


class EmployeeProxy(Employee):

    class Meta:
        proxy = True

    def display(self):
        return self.name.upper()
```

---

## Use Cases

- Custom managers
- Ordering
- Additional methods

---

## Interview Answer

> Proxy models provide a different Python-level behavior for an existing model without creating another database table.

---

# 28. ForeignKey

Represents a **many-to-one** relationship.

---

## Example

```python
class Department(models.Model):
    name = models.CharField(max_length=100)


class Employee(models.Model):
    department = models.ForeignKey(
        Department,
        on_delete=models.CASCADE,
    )
```

---

## Database

```
Department (1)

↓

Employee (Many)
```

---

## Interview Answer

> `ForeignKey` models a many-to-one relationship where multiple child records reference a single parent record.

---

# 29. OneToOneField

Represents a **one-to-one** relationship.

---

## Example

```python
class User(models.Model):
    username = models.CharField(max_length=100)


class Profile(models.Model):
    user = models.OneToOneField(
        User,
        on_delete=models.CASCADE,
    )
```

---

## Use Cases

- User profile
- Employee details
- Preferences

---

## Interview Answer

> `OneToOneField` creates a one-to-one relationship where each record in one table corresponds to exactly one record in another.

---

# 30. ManyToManyField

Represents a many-to-many relationship.

---

## Example

```python
class Student(models.Model):
    name = models.CharField(max_length=100)


class Course(models.Model):
    students = models.ManyToManyField(Student)
```

---

## Database

```
Student

↓

Join Table

↓

Course
```

---

## Interview Answer

> `ManyToManyField` creates a many-to-many relationship using an automatically generated junction table unless a custom through model is specified.

---

# 31. `select_related()`

Optimizes queries by performing SQL joins.

---

## Example

```python
employees = (
    Employee.objects
    .select_related("department")
)
```

---

## SQL

Uses `INNER JOIN` or `LEFT OUTER JOIN` depending on relationship nullability.

---

## Best For

- ForeignKey
- OneToOneField

---

## Interview Answer

> `select_related()` performs SQL joins to retrieve related objects in a single query, making it ideal for `ForeignKey` and `OneToOneField` relationships.

---

# 32. `prefetch_related()`

Executes separate queries and joins results in Python.

---

## Example

```python
courses = (
    Course.objects
    .prefetch_related("students")
)
```

---

## Queries

```
Query 1

Courses

Query 2

Students
```

---

## Best For

- ManyToManyField
- Reverse ForeignKey

---

## Interview Answer

> `prefetch_related()` fetches related objects using separate queries and combines them in memory, making it efficient for many-to-many and reverse relationships.

---

# 33. Difference Between `select_related()` and `prefetch_related()`

| Feature | select_related | prefetch_related |
|----------|----------------|------------------|
| Queries | Single | Multiple |
| SQL Join | Yes | No |
| Python Join | No | Yes |
| ForeignKey | Excellent | Works |
| ManyToMany | Not Supported | Best Choice |

---

## Interview Answer

> `select_related()` performs SQL joins and is efficient for single-valued relationships, while `prefetch_related()` performs separate queries and is better suited for many-to-many and reverse relationships.

---

# 34. Lazy QuerySets

QuerySets are lazy.

No SQL is executed until data is required.

---

## Example

```python
qs = Employee.objects.filter(age__gt=30)
```

No query yet.

---

SQL executes when:

```python
list(qs)

for obj in qs:

len(qs)

bool(qs)

print(qs)
```

---

## Benefits

- Query optimization
- Query chaining
- Reduced unnecessary work

---

## Interview Answer

> Django QuerySets are lazily evaluated, meaning SQL is not executed until the results are actually needed.

---

# 35. QuerySet Evaluation

Evaluation occurs when the QuerySet is consumed.

---

## Triggers

```python
for obj in qs

list(qs)

len(qs)

bool(qs)

first()

last()

count()

exists()

aggregate()
```

---

## Interview Answer

> A QuerySet is evaluated when Django needs actual data, such as during iteration, conversion to a list, or methods like `first()`, `exists()`, or `count()`.

---

# 36. QuerySet Caching

Once evaluated, a QuerySet caches its results.

---

## Example

```python
qs = Employee.objects.all()

list(qs)

list(qs)
```

Only the first evaluation hits the database.

---

## Refresh

```python
qs = Employee.objects.all()
```

creates a new QuerySet.

---

## Interview Answer

> After evaluation, a QuerySet caches its results in memory, preventing repeated database queries when iterated multiple times.

---

# 37. `only()` vs `defer()`

## `only()`

Load only specified fields.

```python
Employee.objects.only("name")
```

---

## `defer()`

Load all fields except specified ones.

```python
Employee.objects.defer("bio")
```

---

## Comparison

| only() | defer() |
|---------|----------|
| Include selected fields | Exclude selected fields |

Deferred fields are fetched lazily if accessed later.

---

## Interview Answer

> `only()` restricts the initially loaded fields, while `defer()` postpones loading specified fields until they are accessed.

---

# 38. `values()` vs `values_list()`

## `values()`

Returns dictionaries.

```python
Employee.objects.values("name", "salary")
```

Output:

```python
[
    {"name": "Alice", "salary": 1000}
]
```

---

## `values_list()`

Returns tuples.

```python
Employee.objects.values_list("name", "salary")
```

Output:

```python
[
    ("Alice", 1000)
]
```

---

## Interview Answer

> `values()` returns dictionaries keyed by field names, whereas `values_list()` returns tuples, making it slightly more memory efficient when field names are unnecessary.

---

# 39. Bulk Create

Insert multiple rows efficiently.

---

## Example

```python
Employee.objects.bulk_create([
    Employee(name="A"),
    Employee(name="B"),
])
```

---

## Advantages

- Fewer SQL statements
- Faster inserts

---

## Limitations

- Does not call `save()`
- `pre_save` and `post_save` signals are not triggered

---

## Interview Answer

> `bulk_create()` inserts multiple objects efficiently using fewer SQL statements, but it bypasses `save()` and model signals.

---

# 40. Bulk Update

Update multiple records efficiently.

---

## Example

```python
Employee.objects.bulk_update(
    employees,
    ["salary"],
)
```

---

## Benefits

- Fewer SQL statements
- Faster updates

---

## Limitations

Like `bulk_create()`, it bypasses `save()` and model signals.

---

## Interview Answer

> `bulk_update()` updates multiple model instances efficiently in bulk while bypassing individual `save()` calls and signals.

---

# 41. F Expressions

Perform database-side operations without fetching values into Python.

---

## Example

```python
from django.db.models import F

Employee.objects.update(
    salary=F("salary") + 5000
)
```

---

## SQL

```sql
UPDATE employee
SET salary = salary + 5000;
```

---

## Benefits

- Atomic updates
- Avoid race conditions
- Better performance

---

## Interview Answer

> `F` expressions allow field values to be referenced directly in database operations, enabling atomic updates and reducing race conditions.

---

# 42. Q Objects

Create complex query conditions.

---

## Example

```python
from django.db.models import Q

Employee.objects.filter(
    Q(age__gt=30) | Q(name="Alice")
)
```

---

## Operators

- `&` → AND
- `|` → OR
- `~` → NOT

---

## Interview Answer

> `Q` objects allow complex query expressions using logical operators such as AND, OR, and NOT.

---

# 43. Subquery

Use one query inside another.

---

## Example

```python
from django.db.models import OuterRef, Subquery

latest_salary = Salary.objects.filter(
    employee=OuterRef("pk")
).values("amount")[:1]

Employee.objects.annotate(
    latest_salary=Subquery(latest_salary)
)
```

---

## Benefits

- Avoid extra queries
- Push computation to the database

---

## Interview Answer

> `Subquery` embeds one SQL query inside another, allowing related values to be selected efficiently without additional round trips.

---

# 44. `Exists()`

Efficiently checks whether related rows exist.

---

## Example

```python
from django.db.models import Exists, OuterRef

Employee.objects.annotate(
    has_salary=Exists(
        Salary.objects.filter(
            employee=OuterRef("pk")
        )
    )
)
```

---

## SQL

Uses the SQL `EXISTS` operator, which often stops scanning after the first match.

---

## Interview Answer

> `Exists()` generates an SQL `EXISTS` clause, providing an efficient way to determine whether related records are present.

---

# 45. Window Functions in Django ORM

Window functions perform calculations across a set of rows without collapsing the result set.

---

## Example

```python
from django.db.models import F, Window
from django.db.models.functions import Rank

Employee.objects.annotate(
    rank=Window(
        expression=Rank(),
        order_by=F("salary").desc(),
    )
)
```

---

## Common Window Functions

- `Rank`
- `DenseRank`
- `RowNumber`
- `Lag`
- `Lead`

---

## Use Cases

- Ranking
- Running totals
- Moving averages
- Analytics
- Leaderboards

---

## Interview Answer

> Django ORM supports SQL window functions through the `Window` expression. They enable advanced analytical queries such as ranking, running totals, and lag/lead calculations while preserving individual rows.

---

# Module Summary

- Django ORM maps Python models to relational database tables and generates SQL automatically.
- ORM improves productivity and portability, while raw SQL offers maximum control for specialized queries.
- A model lifecycle includes object creation, optional validation, `save()`, signals, SQL generation, and persistence.
- Django supports **Abstract Base Classes**, **Multi-table Inheritance**, and **Proxy Models**.
- Relationships are modeled using `ForeignKey`, `OneToOneField`, and `ManyToManyField`.
- `select_related()` optimizes single-valued relationships with SQL joins, while `prefetch_related()` efficiently loads multi-valued relationships using separate queries.
- QuerySets are lazy, evaluated only when needed, and cache results after evaluation.
- `only()` and `defer()` control deferred field loading, while `values()` and `values_list()` return lightweight query results.
- `bulk_create()` and `bulk_update()` improve performance but bypass `save()` and signals.
- Advanced ORM features include `F` expressions, `Q` objects, `Subquery`, `Exists`, and SQL window functions for efficient, expressive database queries.