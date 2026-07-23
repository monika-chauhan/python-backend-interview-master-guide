# Django Interview Master Guide

# Module 1: Django Fundamentals (Questions 1–10)

---

# 1. What is Django?

## Definition

**Django** is a **high-level Python web framework** that enables developers to build secure, scalable, and maintainable web applications rapidly.

It follows the philosophy:

> **"Batteries Included."**

This means Django provides many built-in features instead of requiring third-party libraries.

Examples:

- ORM
- Authentication
- Admin Panel
- Sessions
- Middleware
- Security
- Forms
- Template Engine
- URL Routing
- Caching

---

## Features

- Written in Python
- Open Source
- Follows MTV Architecture
- Built-in ORM
- Auto Admin Interface
- Security Features
- Middleware Support
- Scalable
- Reusable Apps

---

## Companies Using Django

- Instagram
- Pinterest
- Disqus
- Mozilla
- National Geographic

---

## Advantages

- Rapid Development
- Clean Architecture
- Secure by Default
- Excellent ORM
- Large Community
- Highly Scalable

---

## When to Use Django

- Enterprise Applications
- E-commerce
- Banking Systems
- CMS
- ERP
- Social Media
- SaaS Platforms
- REST APIs (with DRF)

---

## Interview Answer

> Django is a high-level Python web framework that follows the MTV architecture and provides a batteries-included approach for building secure, scalable, and maintainable web applications. It includes built-in features such as an ORM, authentication, middleware, an admin interface, and strong security protections, making it suitable for enterprise applications.

---

# 2. Why Django over Flask/FastAPI?

This depends on the project requirements.

---

## Django

### Best For

- Large enterprise applications
- Full-stack web applications
- Admin dashboards
- Authentication-heavy systems
- ERP/CRM
- CMS

---

## Flask

### Best For

- Small applications
- Microservices
- Learning
- Lightweight APIs

Flask provides minimal functionality and requires additional extensions.

---

## FastAPI

### Best For

- High-performance APIs
- Async applications
- AI/ML backends
- Microservices
- Modern REST APIs

---

## Comparison

| Feature | Django | Flask | FastAPI |
|---------|---------|--------|----------|
| ORM | ✅ Built-in | ❌ Extension | ❌ SQLAlchemy/Tortoise |
| Admin Panel | ✅ | ❌ | ❌ |
| Authentication | ✅ | Extension | Extension |
| Async Support | Partial (Improving) | Limited | Excellent |
| Performance | High | High | Very High |
| Batteries Included | ✅ | ❌ | ❌ |
| Learning Curve | Medium | Easy | Medium |

---

## Interview Answer

> I would choose Django for enterprise applications that require authentication, an ORM, an admin panel, and strong security features. Flask is better for lightweight applications, while FastAPI is ideal for high-performance asynchronous APIs and microservices.

---

# 3. Explain MTV Architecture

Django follows the **MTV (Model-Template-View)** architecture.

It is conceptually similar to MVC.

---

## Components

### Model

Represents data and database logic.

```python
class User(models.Model):
    name = models.CharField(max_length=100)
```

---

### Template

Responsible for presentation.

```html
<h1>{{ user.name }}</h1>
```

---

### View

Handles business logic and processes requests.

```python
def home(request):
    return render(request, "home.html")
```

---

## Request Flow

```
Browser

↓

URL

↓

View

↓

Model

↓

Database

↓

View

↓

Template

↓

Response
```

---

## MVC vs MTV

| MVC | MTV |
|------|------|
| Model | Model |
| View (UI) | Template |
| Controller | View |

In Django, the framework itself acts as the controller.

---

## Interview Answer

> Django follows the MTV architecture, where Models manage data, Views contain business logic, and Templates render the presentation layer. Django's URL dispatcher and framework components act as the controller.

---

# 4. Django Project vs Django App

## Django Project

A project is the entire web application.

Example:

```
ecommerce/
```

Contains:

- Settings
- URL configuration
- WSGI/ASGI
- Installed apps

---

## Django App

An app is a reusable module that provides a specific feature.

Examples:

- users
- orders
- payments
- inventory

---

## Example Structure

```
ecommerce/

    settings.py

    urls.py

    apps/

        users/

        products/

        orders/

        payments/
```

---

## Interview Answer

> A Django project represents the complete application, while a Django app is a reusable module implementing a specific business feature. A project can contain multiple apps.

---

# 5. Django Request–Response Lifecycle

Every HTTP request passes through several layers before a response is returned.

---

## Lifecycle

```
Browser

↓

Web Server (Nginx)

↓

Gunicorn / Uvicorn

↓

WSGI / ASGI

↓

Django

↓

Middleware

↓

URL Resolver

↓

View

↓

Model

↓

Database

↓

View

↓

Template / JSON

↓

Middleware

↓

Response
```

---

## Key Steps

1. Browser sends request.
2. Web server forwards request.
3. WSGI/ASGI loads Django.
4. Middleware processes request.
5. URL resolver finds matching view.
6. View executes business logic.
7. ORM interacts with database.
8. Response is created.
9. Middleware processes response.
10. Response is returned.

---

## Interview Answer

> Django processes an HTTP request through the web server, WSGI/ASGI interface, middleware, URL resolver, view, ORM, and template or serializer before returning the response through middleware back to the client.

---

# 6. What Happens Internally When a Request Reaches Django?

Internally, Django executes several components in sequence.

---

## Flow

```
HTTP Request

↓

WSGIHandler / ASGIHandler

↓

Load Middleware

↓

Process Request Middleware

↓

Resolve URL

↓

Execute View

↓

ORM (if required)

↓

Template / JSON Response

↓

Process Response Middleware

↓

Return Response
```

---

## Internal Call Sequence (Simplified)

```python
WSGIHandler.__call__()

↓

get_response()

↓

Middleware Chain

↓

URL Resolver

↓

View Function

↓

HttpResponse
```

---

## Interview Answer

> When a request reaches Django, the WSGI or ASGI handler creates an `HttpRequest` object, executes middleware, resolves the URL, invokes the matching view, performs any database operations, constructs an `HttpResponse`, processes response middleware, and returns the final response.

---

# 7. Explain `manage.py`

## Definition

`manage.py` is Django's command-line utility for administrative tasks.

It sets the correct settings module and delegates commands to Django's management framework.

---

## Common Commands

```bash
python manage.py runserver
```

Start development server.

---

```bash
python manage.py makemigrations
```

Generate migration files.

---

```bash
python manage.py migrate
```

Apply migrations.

---

```bash
python manage.py createsuperuser
```

Create admin user.

---

```bash
python manage.py shell
```

Open Django shell.

---

## Internally

```python
execute_from_command_line(sys.argv)
```

This function loads the project settings and executes the requested command.

---

## Interview Answer

> `manage.py` is Django's command-line utility. It initializes the project settings and provides commands such as running the development server, creating migrations, applying migrations, opening the shell, and managing administrative tasks.

---

# 8. Explain Django Settings

The `settings.py` file contains the project's configuration.

---

## Common Settings

```python
INSTALLED_APPS
```

Registered applications.

---

```python
MIDDLEWARE
```

Middleware chain.

---

```python
DATABASES
```

Database configuration.

---

```python
TEMPLATES
```

Template engine configuration.

---

```python
STATIC_URL
```

Static file location.

---

```python
SECRET_KEY
```

Cryptographic secret.

---

```python
ALLOWED_HOSTS
```

Permitted hostnames.

---

```python
DEBUG
```

Development mode flag.

---

## Best Practices

- Separate development and production settings.
- Store secrets in environment variables.
- Never commit sensitive credentials.
- Use configuration management libraries if needed.

---

## Interview Answer

> `settings.py` centralizes Django's configuration, including installed apps, middleware, database connections, templates, static files, security settings, and environment-specific options.

---

# 9. How Does URL Routing Work?

Django maps incoming URLs to view functions or class-based views.

---

## Example

```python
# urls.py

from django.urls import path
from .views import home

urlpatterns = [
    path("", home),
]
```

---

## View

```python
def home(request):
    return HttpResponse("Hello Django")
```

---

## Internal Flow

```
Request

↓

ROOT_URLCONF

↓

urlpatterns

↓

Matching Path

↓

View
```

---

## Dynamic URLs

```python
path("users/<int:id>/", user_detail)
```

---

## Nested URLs

Project `urls.py`

```python
path("users/", include("users.urls"))
```

App `users/urls.py`

```python
path("<int:id>/", user_detail)
```

---

## Interview Answer

> Django's URL dispatcher matches incoming request paths against `urlpatterns`. Once a matching pattern is found, Django invokes the associated view, optionally passing any captured URL parameters.

---

# 10. How Does Django Start Internally?

When the server starts:

```bash
python manage.py runserver
```

Django performs several initialization steps.

---

## Startup Flow

```
manage.py

↓

Load Settings

↓

django.setup()

↓

App Registry

↓

Load Models

↓

Load Middleware

↓

Configure URL Resolver

↓

Create WSGI/ASGI Application

↓

Development Server Starts
```

---

## Internally

### Step 1

`manage.py`

```python
execute_from_command_line()
```

---

### Step 2

Load settings.

---

### Step 3

Initialize Django.

```python
django.setup()
```

---

### Step 4

Populate installed apps.

---

### Step 5

Import models.

---

### Step 6

Build middleware chain.

---

### Step 7

Initialize URL resolver.

---

### Step 8

Start WSGI/ASGI server.

---

## Production Startup

```
Nginx

↓

Gunicorn / Uvicorn

↓

WSGI / ASGI Application

↓

Django
```

---

## Interview Answer

> Django starts by executing `manage.py`, loading the project settings, calling `django.setup()`, initializing the application registry, importing models, configuring middleware and URL routing, creating the WSGI or ASGI application, and finally starting the development or production server.

---

# Module Summary

- Django is a high-level Python web framework that follows the **MTV architecture** and provides a batteries-included approach.
- Django is well suited for enterprise applications due to its built-in ORM, authentication, admin panel, security, and scalability.
- MTV consists of **Model**, **Template**, and **View**, with Django itself acting as the controller.
- A **Project** is the complete application, while an **App** is a reusable module implementing a specific feature.
- Every request flows through the **web server → WSGI/ASGI → middleware → URL resolver → view → ORM → response**.
- Internally, Django creates an `HttpRequest`, processes middleware, resolves the URL, executes the view, and returns an `HttpResponse`.
- `manage.py` is the command-line utility used for running the server, migrations, and administrative tasks.
- `settings.py` centralizes project configuration such as apps, middleware, databases, templates, and security settings.
- URL routing maps request paths to views using `urlpatterns` and supports dynamic parameters and nested routes.
- During startup, Django loads settings, initializes installed apps, builds middleware, configures URL routing, and creates the WSGI/ASGI application.