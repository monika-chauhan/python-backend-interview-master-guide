# Django Interview Master Guide

# Module 6: Django REST Framework (Questions 81–105)

---

# 81. What is DRF?

## Definition

**Django REST Framework (DRF)** is a powerful toolkit built on top of Django for developing RESTful APIs.

It provides features like:

- Serialization
- Authentication
- Permissions
- Pagination
- Filtering
- Throttling
- Versioning
- Browsable API
- Content Negotiation

---

## Why DRF?

Without DRF:

- Manual JSON conversion
- Manual validation
- Manual authentication
- Manual API documentation

With DRF:

Everything is standardized.

---

## Architecture

```
Client

↓

URL

↓

APIView

↓

Serializer

↓

Model

↓

Database

↓

JSON Response
```

---

## Interview Answer

> Django REST Framework (DRF) is a toolkit for building RESTful APIs in Django. It provides serializers, authentication, permissions, throttling, pagination, versioning, and other features that simplify API development.

---

# 82. APIView vs GenericAPIView

## APIView

Lowest-level DRF view.

Example:

```python
from rest_framework.views import APIView

class UserView(APIView):

    def get(self, request):
        ...
```

Everything must be implemented manually.

---

## GenericAPIView

Adds reusable functionality:

- queryset
- serializer_class
- pagination
- filtering

Example:

```python
class UserList(GenericAPIView):
    queryset = User.objects.all()
```

---

## Comparison

| APIView | GenericAPIView |
|----------|----------------|
| Manual CRUD | Reusable |
| More Boilerplate | Less Boilerplate |
| Full Control | Built-in Features |

---

## Interview Answer

> `APIView` provides complete control over request handling, while `GenericAPIView` adds reusable features such as querysets, serializers, filtering, and pagination.

---

# 83. Generic Views

Generic views combine `GenericAPIView` with mixins to reduce boilerplate.

---

## Example

```python
from rest_framework.generics import ListCreateAPIView

class UserView(ListCreateAPIView):

    queryset = User.objects.all()

    serializer_class = UserSerializer
```

---

## Common Generic Views

- ListAPIView
- RetrieveAPIView
- CreateAPIView
- UpdateAPIView
- DestroyAPIView
- ListCreateAPIView
- RetrieveUpdateDestroyAPIView

---

## Interview Answer

> Generic views combine reusable mixins with `GenericAPIView` to implement common CRUD operations with minimal code.

---

# 84. ViewSets

ViewSets group related CRUD operations into one class.

---

## Example

```python
from rest_framework.viewsets import ModelViewSet

class UserViewSet(ModelViewSet):

    queryset = User.objects.all()

    serializer_class = UserSerializer
```

---

## Automatically Provides

- list()
- create()
- retrieve()
- update()
- partial_update()
- destroy()

---

## Benefits

- Less code
- Consistent APIs
- Easy routing

---

## Interview Answer

> ViewSets group related CRUD actions into a single class, reducing duplication and integrating seamlessly with routers.

---

# 85. Routers

Routers automatically generate URL patterns for ViewSets.

---

## Example

```python
router.register(
    "users",
    UserViewSet
)
```

Generated URLs:

```
GET     /users/
POST    /users/
GET     /users/{id}/
PUT     /users/{id}/
DELETE  /users/{id}/
```

---

## Types

- DefaultRouter
- SimpleRouter

---

## Interview Answer

> Routers automatically generate RESTful URL patterns for ViewSets, eliminating the need to define CRUD routes manually.

---

# 86. Serializers

Serializers convert Python objects into JSON and validate incoming data.

---

## Example

```python
class UserSerializer(serializers.Serializer):

    name = serializers.CharField()

    age = serializers.IntegerField()
```

---

## Responsibilities

- Serialization
- Deserialization
- Validation
- Transformation

---

## Interview Answer

> Serializers convert complex Python objects into JSON and validate incoming request data before creating or updating model instances.

---

# 87. ModelSerializer

Automatically generates serializer fields from a Django model.

---

## Example

```python
class UserSerializer(serializers.ModelSerializer):

    class Meta:
        model = User
        fields = "__all__"
```

---

## Advantages

- Less code
- Automatic validation
- Model integration

---

## Interview Answer

> `ModelSerializer` automatically maps Django model fields to serializer fields, reducing boilerplate and providing built-in validation.

---

# 88. Nested Serializer

Represents related objects within a serializer.

---

## Example

```python
class DepartmentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Department
        fields = ["id", "name"]


class EmployeeSerializer(serializers.ModelSerializer):

    department = DepartmentSerializer()

    class Meta:
        model = Employee
        fields = "__all__"
```

---

## Use Cases

- Read APIs
- Complex object graphs

---

## Interview Answer

> Nested serializers embed related object representations inside a parent serializer, making APIs more expressive for read operations.

---

# 89. Serializer Validation

Validation occurs before saving data.

---

## Field Validation

```python
def validate_age(self, value):

    if value < 18:
        raise serializers.ValidationError()

    return value
```

---

## Object Validation

```python
def validate(self, attrs):
    return attrs
```

---

## Interview Answer

> Serializer validation can be performed at both the field level and the object level to enforce business rules before persistence.

---

# 90. Custom Validators

Reusable validation logic.

---

## Example

```python
def validate_salary(value):

    if value < 0:
        raise serializers.ValidationError()
```

---

## Usage

```python
salary = serializers.IntegerField(
    validators=[validate_salary]
)
```

---

## Interview Answer

> Custom validators encapsulate reusable validation logic that can be applied across multiple serializers or fields.

---

# 91. Pagination

Splits large result sets into manageable pages.

---

## Types

- PageNumberPagination
- LimitOffsetPagination
- CursorPagination

---

## Example

```python
REST_FRAMEWORK = {

    "DEFAULT_PAGINATION_CLASS":
        "rest_framework.pagination.PageNumberPagination",

    "PAGE_SIZE": 20
}
```

---

## Interview Answer

> Pagination limits the number of records returned in each response, improving performance and reducing payload size.

---

# 92. Filtering

Filter query results.

---

## Example

```python
GET /users/?age=25
```

---

```python
filterset_fields = ["age"]
```

---

## Interview Answer

> Filtering allows clients to retrieve only records matching specific criteria using query parameters.

---

# 93. Ordering

Sort API results.

---

## Example

```python
GET /users/?ordering=-salary
```

---

```python
ordering_fields = ["salary"]
```

---

## Interview Answer

> Ordering enables API consumers to sort results by specified fields in ascending or descending order.

---

# 94. Search Filter

Keyword search across fields.

---

## Example

```python
search_fields = ["name"]
```

---

```
GET /users/?search=Alice
```

---

## Interview Answer

> Search filtering performs text-based searches across configured fields using query parameters.

---

# 95. Authentication

Authentication verifies the identity of the client.

---

## Types

- Session Authentication
- Basic Authentication
- Token Authentication
- JWT
- OAuth2

---

## Interview Answer

> Authentication establishes the identity of the requesting user before protected resources are accessed.

---

# 96. Permission Classes

Permissions determine **what an authenticated user is allowed to do**.

---

## Built-in Permissions

- AllowAny
- IsAuthenticated
- IsAdminUser
- IsAuthenticatedOrReadOnly

---

## Custom Permission

```python
from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):

    def has_object_permission(
        self,
        request,
        view,
        obj
    ):
        return obj.user == request.user
```

---

## Interview Answer

> Permission classes enforce authorization rules after authentication, controlling access at the view or object level.

---

# 97. JWT Authentication

Uses signed JSON Web Tokens instead of server-side sessions.

---

## Flow

```
Login

↓

JWT Token

↓

Client Stores Token

↓

Authorization:
Bearer <token>

↓

API

↓

Validate Signature

↓

User
```

---

## Benefits

- Stateless
- Scalable
- Mobile friendly

---

## Interview Answer

> JWT authentication uses signed tokens that clients send with each request, enabling stateless authentication suitable for distributed systems.

---

# 98. OAuth

OAuth is an authorization framework that allows users to grant limited access to third-party applications without sharing credentials.

---

## Example

```
Login with Google

↓

Google OAuth

↓

Access Token

↓

Application
```

---

## Use Cases

- Google Login
- GitHub Login
- Microsoft Login
- Facebook Login

---

## Interview Answer

> OAuth enables delegated authorization, allowing applications to access protected resources on behalf of users without handling their passwords.

---

# 99. Token Authentication

Uses a persistent token stored on the server.

---

## Flow

```
Login

↓

Generate Token

↓

Store in Database

↓

Authorization:
Token <token>

↓

API
```

---

## Difference from JWT

| Token | JWT |
|--------|-----|
| Stored Server-side | Stateless |
| DB Lookup Required | Self-contained |

---

## Interview Answer

> Token authentication authenticates requests using a server-stored token associated with a user, requiring a database lookup for validation.

---

# 100. Versioning

Supports multiple API versions simultaneously.

---

## Types

- URL Versioning
- Namespace Versioning
- Header Versioning
- Query Parameter Versioning

---

## Example

```
/api/v1/users/

/api/v2/users/
```

---

## Interview Answer

> API versioning enables backward compatibility by allowing multiple versions of an API to coexist.

---

# 101. Throttling

Throttling limits request frequency to protect APIs.

---

## Types

- Anonymous
- Authenticated
- Scoped

---

## Example

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_RATES": {
        "anon": "100/day",
        "user": "1000/day",
    }
}
```

---

## Interview Answer

> Throttling limits how frequently clients can access an API, protecting it from abuse and excessive load.

---

# 102. Rate Limiting

## Definition

Rate limiting is the broader concept of controlling request rates.

Throttling is DRF's implementation of rate limiting.

---

## Common Strategies

- Fixed Window
- Sliding Window
- Token Bucket
- Leaky Bucket

---

## Enterprise Tools

- Nginx
- Kong
- Envoy
- API Gateway

---

## Interview Answer

> Rate limiting restricts the number of requests a client can make within a specified period. In DRF, throttling provides an application-level implementation, while gateways often enforce limits at the edge.

---

# 103. Content Negotiation

Determines the response format based on client preferences.

---

## Example

Request Header

```
Accept: application/json
```

Response

```json
{
    "name": "Alice"
}
```

---

## Supported Formats

- JSON
- Browsable API
- XML (via third-party packages)

---

## Interview Answer

> Content negotiation selects the most appropriate response representation by considering the client's `Accept` header and the server's available renderers.

---

# 104. API Exception Handling

DRF converts exceptions into standardized HTTP responses.

---

## Example

```python
from rest_framework.views import exception_handler
```

---

## Custom Handler

```python
def custom_exception_handler(exc, context):

    response = exception_handler(exc, context)

    return response
```

---

## Common Status Codes

- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found
- 500 Internal Server Error

---

## Best Practices

- Return consistent error structures
- Avoid exposing internal details
- Log unexpected exceptions
- Include meaningful error messages

---

## Interview Answer

> DRF provides centralized exception handling that converts Python exceptions into consistent HTTP responses. Developers can customize the global exception handler to standardize API error formats.

---

# 105. API Performance

## Best Practices

### Optimize Database Access

- `select_related()`
- `prefetch_related()`

---

### Return Only Required Fields

```python
.only()

.values()
```

---

### Add Proper Indexes

Optimize filtering and sorting.

---

### Cache Responses

- Redis
- Django Cache Framework

---

### Pagination

Avoid returning thousands of records.

---

### Bulk Operations

```python
bulk_create()

bulk_update()
```

---

### Compression

Enable:

- GZip
- Brotli (via web server/proxy support)

---

### Asynchronous Tasks

Use Celery for:

- Emails
- Notifications
- Reports

---

### Monitoring

- Django Debug Toolbar (development)
- Prometheus
- Grafana
- APM tools (e.g., New Relic, Datadog)

---

## Enterprise API Architecture

```
Client

↓

API Gateway

↓

Load Balancer

↓

DRF

↓

Redis Cache

↓

PostgreSQL

↓

Read Replica

↓

Background Workers (Celery)

↓

Monitoring & Logging
```

---

## Interview Answer

> High-performance DRF APIs minimize database queries, use efficient serializers, paginate large datasets, cache frequently accessed data, leverage asynchronous background processing, and monitor application performance continuously.

---

# Module Summary

- DRF is a powerful framework for building RESTful APIs on top of Django.
- `APIView` offers maximum control, while `GenericAPIView`, generic views, and `ViewSets` reduce boilerplate.
- Routers automatically generate RESTful URL patterns for `ViewSets`.
- Serializers handle serialization, deserialization, and validation, with `ModelSerializer` simplifying model-based APIs.
- Nested serializers enable rich representations of related objects.
- DRF supports field-level, object-level, and reusable custom validation.
- Built-in features include pagination, filtering, ordering, search, authentication, permissions, throttling, versioning, and content negotiation.
- Authentication options include Session, Token, JWT, and OAuth-based flows.
- Permission classes enforce authorization at both the view and object level.
- Production-ready APIs emphasize efficient database access, caching, pagination, asynchronous processing, consistent exception handling, and continuous monitoring.