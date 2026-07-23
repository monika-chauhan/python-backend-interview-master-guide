# Django Interview Master Guide

# Module 5: Migrations (Questions 66–80)

---

# 66. What is Migration?

## Definition

A **Migration** is Django's version control system for database schemas.

It records changes made to Django models and translates them into SQL statements that modify the database structure.

Instead of writing SQL manually, developers modify models, and Django generates migration files.

---

## Example

```python
class Employee(models.Model):
    name = models.CharField(max_length=100)
```

After adding:

```python
salary = models.IntegerField(default=0)
```

Run:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Generated SQL

```sql
ALTER TABLE employee
ADD COLUMN salary INTEGER;
```

---

## Advantages

- Version controlled
- Team collaboration
- Rollback support
- Database independent
- Automatic SQL generation

---

## Interview Answer

> Django migrations are version-controlled Python files that describe database schema changes. Django generates SQL from these files and applies it consistently across different environments.

---

# 67. How Migration Works Internally?

When a model changes, Django compares the current models with the last recorded migration state.

---

## Internal Flow

```
Model Changed

↓

makemigrations

↓

Model State Comparison

↓

Migration File

↓

migrate

↓

Migration Executor

↓

Schema Editor

↓

SQL

↓

Database
```

---

## Components

- Migration Autodetector
- Migration Loader
- Migration Graph
- Migration Executor
- Schema Editor

---

## Interview Answer

> Django compares the current model definitions with the migration history, generates migration operations, builds a dependency graph, converts them into SQL through the Schema Editor, and records the applied migration in the database.

---

# 68. Migration Graph

## Definition

Django maintains a **Directed Acyclic Graph (DAG)** of migration dependencies.

---

## Example

```
0001_initial

↓

0002_add_salary

↓

0003_add_department

↓

0004_add_index
```

Each migration depends on previous migrations.

---

## Benefits

- Correct execution order
- Dependency management
- Parallel app migrations
- Conflict detection

---

## Interview Answer

> The migration graph is a directed acyclic graph that tracks dependencies between migrations, ensuring they are executed in the correct order.

---

# 69. Schema Migration

Schema migrations modify the database structure.

---

## Examples

- Add table
- Drop table
- Add column
- Remove column
- Rename column
- Add index
- Add constraint

---

## Example

```python
migrations.AddField(
    model_name="employee",
    name="salary",
    field=models.IntegerField(default=0),
)
```

---

## Interview Answer

> Schema migrations change the structure of the database by creating, altering, or deleting tables, columns, indexes, or constraints.

---

# 70. Data Migration

Data migrations modify the data stored in tables.

---

## Example

```python
from django.db import migrations


def update_salary(apps, schema_editor):
    Employee = apps.get_model("app", "Employee")

    Employee.objects.update(salary=1000)


class Migration(migrations.Migration):

    operations = [
        migrations.RunPython(update_salary)
    ]
```

---

## Use Cases

- Populate new fields
- Data cleanup
- Data transformation
- Default values

---

## Interview Answer

> Data migrations update existing records using Python code, allowing data transformations during schema evolution.

---

# 71. Squash Migrations

## Problem

Projects may accumulate hundreds of migrations.

Example:

```
0001

0002

...

0150
```

---

## Solution

```bash
python manage.py squashmigrations app 0150
```

Produces:

```
0001_squashed_0150.py
```

---

## Benefits

- Faster setup
- Cleaner migration history
- Easier onboarding

---

## Interview Answer

> Squashing combines multiple migrations into a single migration while preserving the resulting schema, reducing migration history complexity.

---

# 72. Fake Migration

Marks a migration as applied without executing SQL.

---

## Command

```bash
python manage.py migrate --fake
```

---

## Use Cases

- Existing database already matches schema
- Manual database changes
- Legacy projects

---

## Caution

Incorrect use may cause migration history to diverge from the actual database schema.

---

## Interview Answer

> A fake migration updates Django's migration history without executing SQL, typically used when the database schema already reflects the intended changes.

---

# 73. Reverse Migration

Rollback to a previous migration.

---

## Example

```bash
python manage.py migrate app 0003
```

---

## Flow

```
Current

↓

0005

↓

Rollback

↓

0003
```

---

## Notes

Not all migrations are reversible. Operations such as dropping a table or executing irreversible custom SQL may require explicit reverse logic.

---

## Interview Answer

> Reverse migration rolls the database back to a previous migration state by executing reverse operations where available.

---

# 74. Merge Migration

Occurs when multiple developers create migrations from the same parent.

---

## Example

```
0005

↙      ↘

0006_A 0006_B

↓

Merge

↓

0007_merge
```

---

## Command

```bash
python manage.py makemigrations --merge
```

---

## Interview Answer

> Merge migrations resolve parallel migration branches by creating a new migration that depends on both conflicting branches.

---

# 75. Migration Dependencies

Each migration specifies its prerequisites.

---

## Example

```python
dependencies = [
    ("users", "0003_initial")
]
```

---

## Why?

Ensures:

- Correct execution order
- Cross-app consistency
- Safe schema evolution

---

## Interview Answer

> Migration dependencies ensure migrations are executed only after their required predecessor migrations have been applied.

---

# 76. Custom Migration

Developers can write custom migration operations.

---

## Example

```python
operations = [

    migrations.RunSQL(
        """
        CREATE INDEX idx_salary
        ON employee(salary);
        """
    )
]
```

---

## Uses

- Vendor-specific SQL
- Database functions
- Triggers
- Stored procedures

---

## Interview Answer

> Custom migrations allow developers to execute operations beyond Django's built-in migration operations, including raw SQL and custom Python logic.

---

# 77. RunPython Migration

Executes Python code during migration.

---

## Example

```python
def forwards(apps, schema_editor):

    Employee = apps.get_model(
        "app",
        "Employee"
    )

    Employee.objects.update(
        active=True
    )


operations = [

    migrations.RunPython(forwards)
]
```

---

## Reversible Version

```python
operations = [
    migrations.RunPython(
        forwards,
        reverse_code=backwards
    )
]
```

---

## Interview Answer

> `RunPython` executes custom Python logic during migrations and is commonly used for data transformations. Providing a `reverse_code` function makes the migration reversible.

---

# 78. Migration Conflicts

Occurs when multiple migrations have the same parent.

---

## Example

Developer A

```
0005_add_salary
```

Developer B

```
0005_add_email
```

Conflict!

---

## Solution

```bash
python manage.py makemigrations --merge
```

---

## Prevention

- Pull latest changes
- Rebase before creating migrations
- Coordinate schema changes

---

## Interview Answer

> Migration conflicts arise when different migration branches diverge from the same parent migration. They are typically resolved by creating a merge migration.

---

# 79. Zero Downtime Migration

## Goal

Deploy schema changes without interrupting application availability.

---

## Safe Strategy

### Step 1

Add nullable column.

```python
email = models.EmailField(
    null=True
)
```

---

### Step 2

Deploy application.

---

### Step 3

Backfill data in batches.

---

### Step 4

Update application to use the new column.

---

### Step 5

Make the field non-nullable if required.

---

## Avoid

- Long table locks
- Dropping columns immediately
- Renaming columns directly in production
- Large blocking transactions

---

## Interview Answer

> Zero-downtime migrations use incremental schema changes, backward-compatible deployments, batched data backfills, and staged rollouts to avoid blocking production traffic.

---

# 80. Large Table Migration

Large tables require special care to avoid locks and downtime.

---

## Best Practices

### Batch Updates

```python
queryset.iterator(chunk_size=1000)
```

---

### Bulk Update

```python
bulk_update()
```

---

### Add Indexes Carefully

For PostgreSQL, create large indexes concurrently when appropriate to reduce locking.

---

### Background Jobs

Use:

- Celery
- Kafka
- Cron

for data backfills.

---

### Avoid

```python
UPDATE

10 Million Rows
```

in one transaction.

---

## Enterprise Strategy

```
Deploy New Code

↓

Add Nullable Column

↓

Background Data Migration

↓

Verify Data

↓

Switch Reads/Writes

↓

Remove Old Column
```

---

## Interview Answer

> Large-table migrations should avoid long-running locks by using incremental schema changes, batched data processing, background jobs, concurrent index creation where supported, and staged application deployments.

---

# Module Summary

- Django migrations provide version-controlled database schema management.
- Internally, Django compares model states, generates migration operations, builds a migration graph, and executes SQL through the Schema Editor.
- The migration graph is a dependency-based DAG that guarantees correct execution order.
- Schema migrations modify database structures, while data migrations transform existing data.
- Squash migrations simplify long migration histories.
- Fake migrations mark migrations as applied without executing SQL.
- Reverse migrations roll back schema changes when reversible operations exist.
- Merge migrations resolve parallel migration branches and dependency conflicts.
- Dependencies ensure migrations execute in the correct sequence across apps.
- Custom migrations and `RunPython` enable advanced schema and data operations.
- Zero-downtime and large-table migrations rely on backward-compatible changes, batching, staged deployments, and background processing to minimize production impact.