# Django Interview Master Guide

# Module 8: Middleware (Questions 121–130)

---

# 121. What is Middleware?

## Definition

**Middleware** is a layer of code that sits between the web server and Django views. It processes every incoming request before it reaches the view and every outgoing response before it is sent back to the client.

Think of middleware as a chain of filters through which every request and response passes.

---

## Architecture

```
Client

↓

Web Server

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

## Responsibilities

- Authentication
- Session management
- Security
- Logging
- Request modification
- Response modification
- Exception handling
- Performance monitoring

---

## Example

```python
class SimpleMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):

        print("Before View")

        response = self.get_response(request)

        print("After View")

        return response
```

---

## Interview Answer

> Middleware is a request/response processing layer in Django that executes before and after views. It is commonly used for authentication, security, logging, session management, exception handling, and performance monitoring.

---

# 122. Middleware Execution Flow

## Complete Request Lifecycle

```
Client

↓

WSGI / ASGI

↓

SecurityMiddleware

↓

SessionMiddleware

↓

CommonMiddleware

↓

CsrfViewMiddleware

↓

AuthenticationMiddleware

↓

MessageMiddleware

↓

Custom Middleware

↓

View

↓

Custom Middleware

↓

MessageMiddleware

↓

AuthenticationMiddleware

↓

CsrfViewMiddleware

↓

CommonMiddleware

↓

SessionMiddleware

↓

SecurityMiddleware

↓

Response
```

---

## Important Point

Request processing occurs **top to bottom**, while response processing occurs **bottom to top**.

---

## Simplified Example

```python
MIDDLEWARE = [
    "A",
    "B",
    "C",
]
```

Execution:

```
Request

A

↓

B

↓

C

↓

View

↓

C

↓

B

↓

A

↓

Response
```

---

## Interview Answer

> Middleware executes sequentially for incoming requests and in reverse order for outgoing responses. Each middleware can modify the request, response, or terminate processing early.

---

# 123. Built-in Middleware

Django provides several built-in middleware components.

---

## Common Built-in Middleware

| Middleware | Purpose |
|------------|----------|
| SecurityMiddleware | Security headers |
| SessionMiddleware | Session management |
| CommonMiddleware | Common HTTP operations |
| CsrfViewMiddleware | CSRF protection |
| AuthenticationMiddleware | User authentication |
| MessageMiddleware | Flash messages |
| Clickjacking Middleware | X-Frame-Options protection |

---

## Default Configuration

```python
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
]
```

---

## Interview Answer

> Django includes built-in middleware for security, sessions, authentication, CSRF protection, messaging, and other common web application concerns.

---

# 124. Authentication Middleware

## Purpose

Associates the authenticated user with each request.

---

## Flow

```
Request

↓

Session Cookie

↓

AuthenticationMiddleware

↓

Load User

↓

request.user

↓

View
```

---

## Example

```python
def dashboard(request):

    if request.user.is_authenticated:
        ...
```

---

## Dependency

AuthenticationMiddleware depends on SessionMiddleware because it retrieves the authenticated user's ID from the session.

---

## Interview Answer

> AuthenticationMiddleware populates `request.user` by retrieving authentication information from the session or configured authentication backend.

---

# 125. Session Middleware

## Purpose

Loads and saves session data.

---

## Flow

```
Request

↓

Session Cookie

↓

Session Store

↓

request.session

↓

View

↓

Save Session

↓

Response
```

---

## Example

```python
request.session["username"] = "Alice"
```

---

## Supported Backends

- Database
- Cache
- File
- Signed Cookies

---

## Interview Answer

> SessionMiddleware loads session data at the beginning of the request and persists any session changes before the response is returned.

---

# 126. Custom Middleware

## Example

```python
class RequestTimerMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):

        print("Request Started")

        response = self.get_response(request)

        print("Request Finished")

        return response
```

---

## Register Middleware

```python
MIDDLEWARE = [
    ...
    "app.middleware.RequestTimerMiddleware",
]
```

---

## Use Cases

- Logging
- Request timing
- Tenant identification
- Request tracing
- Header modification
- Feature flags

---

## Interview Answer

> Custom middleware enables developers to implement application-wide request and response processing logic that executes for every request.

---

# 127. Logging Middleware

## Purpose

Logs request and response information.

---

## Example

```python
import logging

logger = logging.getLogger(__name__)


class LoggingMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):

        logger.info(
            "%s %s",
            request.method,
            request.path,
        )

        response = self.get_response(request)

        logger.info(
            "Status: %s",
            response.status_code,
        )

        return response
```

---

## Production Logging

Capture:

- Request ID
- User ID
- URL
- Method
- Status code
- Response time
- Client IP (respecting proxy configuration)

---

## Interview Answer

> Logging middleware records request and response details, providing valuable information for debugging, auditing, monitoring, and observability.

---

# 128. Exception Middleware

## Purpose

Handles unexpected exceptions centrally.

---

## Example

```python
class ExceptionMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):

        try:
            return self.get_response(request)

        except Exception:
            return HttpResponse(
                "Internal Server Error",
                status=500,
            )
```

---

## Better Practice

Log the exception and return a user-friendly error response without exposing internal implementation details.

---

## Benefits

- Consistent error handling
- Centralized logging
- Improved reliability

---

## Interview Answer

> Exception middleware intercepts unhandled exceptions, logs them, and returns standardized error responses while preventing sensitive information from being exposed.

---

# 129. Performance Middleware

## Purpose

Measures application performance.

---

## Example

```python
import time


class PerformanceMiddleware:

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):

        start = time.time()

        response = self.get_response(request)

        duration = time.time() - start

        print(duration)

        return response
```

---

## Enterprise Metrics

- Request duration
- Database query count
- Slow requests
- Memory usage
- CPU utilization (via monitoring tools)

---

## Integration

- Prometheus
- Grafana
- OpenTelemetry
- Datadog
- New Relic

---

## Interview Answer

> Performance middleware measures request latency and can collect metrics that integrate with monitoring systems to identify performance bottlenecks.

---

# 130. Middleware vs Decorator

| Middleware | Decorator |
|------------|-----------|
| Runs for every request | Runs only for decorated views |
| Global scope | Local scope |
| Configured in `settings.py` | Applied in code |
| Request & Response | Usually View only |
| Good for logging, authentication, security | Good for authorization, caching, validation |

---

## Middleware Example

```python
class LoggingMiddleware:
    ...
```

Runs for all requests.

---

## Decorator Example

```python
from django.contrib.auth.decorators import login_required


@login_required
def dashboard(request):
    ...
```

Runs only for the `dashboard` view.

---

## When to Use Middleware

- Authentication
- Logging
- Security headers
- Sessions
- Monitoring
- Request tracing

---

## When to Use Decorators

- Login required
- Permission checks
- Rate limiting (view-specific)
- Caching
- Validation

---

## Interview Answer

> Middleware applies globally to every request and response, making it suitable for cross-cutting concerns. Decorators apply to individual views and are better suited for view-specific functionality such as authentication or permission checks.

---

# Module Summary

- Middleware is a request/response processing layer that surrounds Django views.
- Requests flow through middleware from top to bottom, while responses travel in reverse order.
- Django provides built-in middleware for security, sessions, authentication, CSRF protection, messaging, and common HTTP behavior.
- AuthenticationMiddleware populates `request.user`, while SessionMiddleware manages session persistence.
- Custom middleware enables centralized processing such as logging, request tracing, feature flags, and tenant resolution.
- Logging middleware improves observability, exception middleware centralizes error handling, and performance middleware enables monitoring and metrics collection.
- Middleware addresses application-wide concerns, whereas decorators provide functionality for individual views.