# Django Interview Master Guide

# Module 2: Django Internals (Questions 11–20)

---

# 11. Explain Django WSGI

## What is WSGI?

**WSGI (Web Server Gateway Interface)** is the standard interface between a Python web application and a web server.

It allows web servers like:

- Gunicorn
- uWSGI
- mod_wsgi

to communicate with Django.

---

## Why WSGI?

Without WSGI:

```
Nginx

❌

Cannot directly execute Python code
```

WSGI acts as a bridge.

---

## Architecture

```
Browser

↓

Nginx

↓

Gunicorn

↓

WSGI

↓

Django

↓

Response
```

---

## wsgi.py

```python
from django.core.wsgi import get_wsgi_application

application = get_wsgi_application()
```

`application` is the WSGI callable used by Gunicorn or uWSGI.

---

## Request Flow

```
HTTP Request

↓

Gunicorn

↓

application(request)

↓

WSGIHandler

↓

Middleware

↓

View

↓

Response
```

---

## Production Example

```bash
gunicorn myproject.wsgi
```

---

## Interview Answer

> WSGI (Web Server Gateway Interface) is the standard interface between synchronous Python web applications and web servers. Django exposes a WSGI application through `wsgi.py`, allowing servers such as Gunicorn or uWSGI to forward HTTP requests to Django.

---

# 12. Explain ASGI

## What is ASGI?

**ASGI (Asynchronous Server Gateway Interface)** is the successor to WSGI.

It supports:

- Async requests
- WebSockets
- HTTP/2
- Long-lived connections
- Background tasks

---

## Why ASGI?

WSGI only supports synchronous request handling.

ASGI supports:

```python
async def
await
```

---

## Architecture

```
Browser

↓

Nginx

↓

Uvicorn

↓

ASGI

↓

Django

↓

Response
```

---

## asgi.py

```python
from django.core.asgi import get_asgi_application

application = get_asgi_application()
```

---

## Async View

```python
async def home(request):
    return HttpResponse("Hello")
```

---

## WSGI vs ASGI

| Feature | WSGI | ASGI |
|----------|------|------|
| Sync | ✅ | ✅ |
| Async | ❌ | ✅ |
| WebSockets | ❌ | ✅ |
| Long Connections | ❌ | ✅ |
| HTTP/2 | ❌ | ✅ |

---

## Interview Answer

> ASGI is the asynchronous interface for Python web applications. It extends WSGI by supporting asynchronous views, WebSockets, and long-lived connections. Django exposes an ASGI application through `asgi.py`, typically served using Uvicorn or Daphne.

---

# 13. How Does Django Process Middleware?

## What is Middleware?

Middleware is a chain of components that process requests **before** and responses **after** the view executes.

---

## Request Flow

```
Request

↓

Middleware 1

↓

Middleware 2

↓

Middleware 3

↓

View

↓

Middleware 3

↓

Middleware 2

↓

Middleware 1

↓

Response
```

---

## Example Middleware

```python
class MyMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        print("Before View")

        response = self.get_response(request)

        print("After View")

        return response
```

---

## Built-in Middleware

- SecurityMiddleware
- SessionMiddleware
- CommonMiddleware
- CsrfViewMiddleware
- AuthenticationMiddleware
- MessageMiddleware

---

## Interview Answer

> Django middleware forms a processing pipeline. Each middleware receives the request, performs pre-processing, calls `get_response()` to continue the chain, and then performs post-processing on the response before returning it.

---

# 14. Explain URL Resolver Internally

The URL Resolver maps incoming request paths to the correct view.

---

## Example

```python
urlpatterns = [
    path("users/", views.users),
]
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

Regex Matching

↓

View
```

---

## Internally

Django creates a `URLResolver` object.

It recursively searches:

```
urlpatterns

↓

include()

↓

Nested URLConf

↓

Matching Pattern
```

---

## Example

```python
path("users/", include("users.urls"))
```

---

## Interview Answer

> Django's URL resolver reads the project's URL configuration, recursively traverses nested URL patterns, matches the incoming path against registered routes, and returns the corresponding view and captured parameters.

---

# 15. How Does Django Find Views?

After URL resolution, Django imports the associated view.

---

## Example

```python
urlpatterns = [
    path("home/", views.home),
]
```

---

## Internally

```
URL Pattern

↓

Import View

↓

Call View(request)
```

---

## Function-Based View

```python
def home(request):
    return HttpResponse("Hello")
```

---

## Class-Based View

```python
class HomeView(View):

    def get(self, request):
        ...
```

Internally:

```python
HomeView.as_view()
```

creates a callable view.

---

## Interview Answer

> Once a URL pattern is matched, Django imports the referenced function or class-based view. For class-based views, `as_view()` returns a callable that instantiates the class and dispatches the request to the appropriate HTTP method.

---

# 16. What is `get_response`?

`get_response` is the next callable in Django's request-processing chain.

---

## Simplified Middleware

```python
class Middleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):

        response = self.get_response(request)

        return response
```

---

## Internal Flow

```
Middleware A

↓

get_response()

↓

Middleware B

↓

get_response()

↓

View

↓

Response
```

---

## Why Important?

It allows middleware to wrap the remaining request-processing pipeline.

---

## Interview Answer

> `get_response` is the callable passed to middleware that invokes the next middleware or the final view. It allows middleware to execute logic before and after the remaining request-processing chain.

---

# 17. Explain Django App Registry

The **App Registry** stores metadata about every installed application.

---

## Purpose

Keeps track of:

- Installed apps
- Models
- App configurations

---

## Internally

```
INSTALLED_APPS

↓

AppConfig

↓

App Registry

↓

Loaded Models
```

---

## Example

```python
from django.apps import apps

apps.get_models()
```

---

## Uses

- Model discovery
- Signal registration
- Admin registration
- ContentTypes

---

## Interview Answer

> The App Registry maintains information about all installed applications and their models. It is initialized during `django.setup()` and is used throughout Django for model discovery, signals, admin registration, and application configuration.

---

# 18. What Happens During `django.setup()`?

`django.setup()` initializes the Django application.

---

## Steps

```
Load Settings

↓

Initialize Logging

↓

Populate App Registry

↓

Load Models

↓

Import AppConfig

↓

Register Signals

↓

Ready
```

---

## Example

```python
import django

django.setup()
```

---

## Why Needed?

Outside `manage.py` or WSGI/ASGI (e.g., standalone scripts), Django components are unavailable until `django.setup()` is called.

---

## Interview Answer

> `django.setup()` initializes Django by loading settings, configuring logging, populating the application registry, importing models, and preparing the framework for handling requests or executing management tasks.

---

# 19. Explain Django Signals Internally

Signals provide a way for objects to notify other parts of the application when events occur.

---

## Example

```
User Saved

↓

Signal Sent

↓

Receivers Executed
```

---

## Example

```python
from django.db.models.signals import post_save
from django.dispatch import receiver


@receiver(post_save, sender=User)
def notify(sender, instance, created, **kwargs):

    if created:
        print("Email Sent")
```

---

## Internally

```
Model.save()

↓

post_save.send()

↓

Signal Dispatcher

↓

Receiver Functions
```

---

## Common Signals

- pre_save
- post_save
- pre_delete
- post_delete
- m2m_changed

---

## Best Practice

Keep signal handlers lightweight.

Avoid placing critical business logic exclusively in signals because execution order and side effects can become difficult to reason about.

---

## Interview Answer

> Django signals implement the Observer pattern. When an event such as `post_save` occurs, Django's signal dispatcher invokes all registered receivers for that signal and sender.

---

# 20. How Does Django Load Installed Apps?

Django loads applications listed in:

```python
INSTALLED_APPS
```

during startup.

---

## Example

```python
INSTALLED_APPS = [

    "django.contrib.admin",

    "users",

    "orders",
]
```

---

## Internal Flow

```
Read INSTALLED_APPS

↓

Import AppConfig

↓

Create AppConfig Objects

↓

Populate App Registry

↓

Import Models

↓

Call AppConfig.ready()
```

---

## `ready()`

```python
class UsersConfig(AppConfig):

    name = "users"

    def ready(self):
        import users.signals
```

This is commonly used to register signals.

---

## Startup Sequence

```
settings.py

↓

INSTALLED_APPS

↓

AppConfig

↓

Models

↓

Signals

↓

Application Ready
```

---

## Interview Answer

> During startup, Django reads `INSTALLED_APPS`, creates an `AppConfig` for each application, populates the App Registry, imports models, and finally calls each app's `ready()` method, where tasks such as signal registration are typically performed.

---

# Module Summary

- **WSGI** is the synchronous interface between Django and web servers such as Gunicorn.
- **ASGI** extends Django with asynchronous capabilities, including async views and WebSockets.
- Middleware forms a request/response pipeline around views using the `get_response` callable.
- The URL Resolver recursively matches incoming request paths to registered URL patterns and views.
- Django locates function-based views directly and class-based views through the `as_view()` callable.
- `get_response` represents the next component in the middleware chain or the final view.
- The **App Registry** tracks installed applications, models, and configuration metadata.
- `django.setup()` initializes settings, logging, the App Registry, models, and application configuration.
- Django signals implement the Observer pattern, allowing receivers to react to framework events.
- Installed apps are loaded by processing `INSTALLED_APPS`, importing `AppConfig`, loading models, and executing each app's `ready()` method.