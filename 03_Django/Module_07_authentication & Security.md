# Django Interview Master Guide

# Module 7: Authentication & Security (Questions 106–120)

---

# 106. Django Authentication Flow

## What is Authentication?

Authentication is the process of verifying a user's identity before granting access to protected resources.

Django provides a built-in authentication framework (`django.contrib.auth`) that supports users, groups, permissions, sessions, and authentication backends.

---

## Authentication Flow

```
Client

↓

POST /login

↓

Authentication Backend

↓

Verify Username & Password

↓

User Object

↓

Create Session / Generate JWT

↓

Return Response

↓

Subsequent Requests

↓

Authenticate User

↓

Permission Check

↓

View Execution
```

---

## Login Example

```python
from django.contrib.auth import authenticate, login

user = authenticate(
    request,
    username=username,
    password=password
)

if user:
    login(request, user)
```

---

## Internals

1. User submits credentials.
2. `authenticate()` checks configured authentication backends.
3. Password is verified using Django's password hasher.
4. On success, `login()` creates a session.
5. Session ID is stored in a cookie.
6. Future requests use the session cookie to identify the user.

---

## Interview Answer

> Django's authentication flow verifies user credentials through authentication backends, creates a session upon successful login, stores the session ID in a cookie, and authenticates subsequent requests using that session.

---

# 107. Sessions

## What is a Session?

A session stores user-specific data on the server while the client only stores a session identifier.

---

## Flow

```
User Login

↓

Create Session

↓

Store Session in DB/Cache

↓

Return Session ID Cookie

↓

Next Request

↓

Load Session

↓

Authenticated User
```

---

## Session Storage Options

- Database
- Cache (Redis, Memcached)
- File system
- Signed cookies

---

## Advantages

- Server-side control
- Easy logout
- Secure session invalidation

---

## Disadvantages

- Requires server-side storage
- Harder to scale without shared session storage

---

## Interview Answer

> Sessions store authentication state on the server while clients store only a session identifier. Django supports multiple session backends, including databases, caches, files, and signed cookies.

---

# 108. Cookies

## What are Cookies?

Cookies are small pieces of data stored in the user's browser.

---

## Types

### Session Cookies

Deleted when the browser closes.

### Persistent Cookies

Remain until expiration.

---

## Example

```
Set-Cookie:

sessionid=abc123
```

---

## Secure Cookie Settings

```python
SESSION_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
SESSION_COOKIE_SAMESITE = "Lax"
```

---

## Best Practices

- Use HTTPS
- Enable HttpOnly
- Set SameSite
- Limit lifetime

---

## Interview Answer

> Cookies store small pieces of client-side data such as session identifiers. Security is improved by using HTTPS, HttpOnly, Secure, and SameSite attributes.

---

# 109. CSRF (Cross-Site Request Forgery)

## Definition

A CSRF attack tricks an authenticated user into submitting an unintended request.

---

## Example Attack

```
Victim Logged In

↓

Visits Malicious Website

↓

Hidden POST Request

↓

Bank Transfer
```

---

## Django Protection

- CSRF middleware
- CSRF token
- Token validation

---

## Template Example

```html
<form method="post">
    {% csrf_token %}
</form>
```

---

## Interview Answer

> CSRF attacks exploit authenticated users by forcing unwanted requests. Django prevents them using CSRF tokens validated by `CsrfViewMiddleware`.

---

# 110. XSS (Cross-Site Scripting)

## Definition

XSS occurs when attackers inject malicious JavaScript into web pages.

---

## Example

```html
<script>alert("Hacked")</script>
```

---

## Types

- Stored XSS
- Reflected XSS
- DOM-based XSS

---

## Django Protection

- Automatic template escaping
- Content Security Policy (CSP)
- Input validation
- Output encoding

---

## Avoid

```python
mark_safe(user_input)
```

unless absolutely necessary.

---

## Interview Answer

> XSS attacks inject malicious scripts into web pages. Django mitigates XSS through automatic template escaping, while developers should validate input and avoid rendering untrusted HTML.

---

# 111. SQL Injection

## Definition

SQL Injection occurs when untrusted input alters SQL queries.

---

## Unsafe Example

```python
query = f"""
SELECT *
FROM users
WHERE username = '{username}'
"""
```

---

## Safe ORM Example

```python
User.objects.filter(username=username)
```

---

## Safe Raw SQL

```python
cursor.execute(
    "SELECT * FROM users WHERE username=%s",
    [username],
)
```

---

## Interview Answer

> SQL injection occurs when user input modifies SQL statements. Django ORM and parameterized queries prevent this by separating SQL code from user-provided values.

---

# 112. Clickjacking

## Definition

Clickjacking tricks users into clicking hidden elements inside an embedded page.

---

## Attack

```
Malicious Site

↓

Invisible iframe

↓

Victim Clicks

↓

Sensitive Action
```

---

## Django Protection

```python
X_FRAME_OPTIONS = "DENY"
```

or

```python
X_FRAME_OPTIONS = "SAMEORIGIN"
```

---

## Interview Answer

> Clickjacking attacks embed a website inside an invisible frame to deceive users. Django protects against this using the `X-Frame-Options` response header.

---

# 113. CORS (Cross-Origin Resource Sharing)

## Definition

CORS controls whether a browser allows requests between different origins.

---

## Example

Frontend:

```
https://app.example.com
```

Backend:

```
https://api.example.com
```

---

## Django Package

```
django-cors-headers
```

---

## Configuration

```python
CORS_ALLOWED_ORIGINS = [
    "https://app.example.com",
]
```

---

## Interview Answer

> CORS is a browser security mechanism that controls cross-origin requests. Django commonly uses the `django-cors-headers` package to configure trusted origins.

---

# 114. JWT vs Session

| Feature | JWT | Session |
|----------|-----|----------|
| Server Storage | No | Yes |
| Scalability | Excellent | Moderate |
| Stateless | Yes | No |
| Logout | More complex | Easy |
| Mobile APIs | Excellent | Less common |
| Browser Apps | Common | Excellent |

---

## When to Use

### JWT

- Mobile apps
- Microservices
- Distributed systems

### Session

- Traditional web applications
- Server-rendered Django apps

---

## Interview Answer

> JWT is stateless and well suited for APIs and distributed systems, while session authentication is stateful and ideal for traditional server-rendered web applications.

---

# 115. Refresh Tokens

## Purpose

Access tokens should be short-lived.

Refresh tokens allow clients to obtain new access tokens without requiring users to log in again.

---

## Flow

```
Login

↓

Access Token (15 min)

Refresh Token (7 days)

↓

Access Token Expires

↓

Refresh Endpoint

↓

New Access Token
```

---

## Benefits

- Better security
- Short-lived access tokens
- Improved user experience

---

## Interview Answer

> Refresh tokens enable clients to obtain new access tokens after expiration, reducing the need for repeated user logins while keeping access tokens short-lived.

---

# 116. OAuth2

## Definition

OAuth2 is an authorization framework for delegated access.

---

## Flow

```
Application

↓

Google Login

↓

Authorization Code

↓

Access Token

↓

User Information
```

---

## Grant Types

- Authorization Code
- Client Credentials
- Refresh Token
- Device Authorization (for limited-input devices)

---

## Use Cases

- Google Login
- Microsoft Login
- GitHub Login

---

## Interview Answer

> OAuth2 enables delegated authorization, allowing applications to access protected resources on behalf of users without exposing user credentials.

---

# 117. Azure AD SSO

## What is Azure AD SSO?

Azure Active Directory (Microsoft Entra ID) provides enterprise Single Sign-On (SSO).

---

## Flow

```
User

↓

Application

↓

Azure AD

↓

Authenticate

↓

ID Token

↓

Application
```

---

## Protocols

- OAuth2
- OpenID Connect (OIDC)
- SAML

---

## Benefits

- Centralized identity
- Multi-factor authentication
- Enterprise security
- Single sign-on

---

## Interview Answer

> Azure AD SSO integrates enterprise identity management using protocols such as OpenID Connect, OAuth2, or SAML, allowing users to authenticate once and access multiple applications securely.

---

# 118. RBAC (Role-Based Access Control)

## Definition

Access is granted based on user roles.

---

## Example

```
Admin

↓

Manager

↓

Employee

↓

Guest
```

---

## Django Implementation

- Groups
- Permissions
- Custom roles

---

## Example

```python
if request.user.has_perm(
    "employee.change_employee"
):
    ...
```

---

## Interview Answer

> RBAC assigns permissions to roles rather than individual users, simplifying authorization management and improving scalability.

---

# 119. Object-Level Permissions

## Definition

Controls access to individual objects rather than entire models.

---

## Example

```
User A

↓

Owns Invoice

↓

Can Edit

User B

↓

Cannot Edit
```

---

## DRF Example

```python
class IsOwner(BasePermission):

    def has_object_permission(
        self,
        request,
        view,
        obj
    ):
        return obj.owner == request.user
```

---

## Use Cases

- Documents
- Orders
- Profiles
- Projects

---

## Interview Answer

> Object-level permissions authorize access to specific model instances, ensuring users can only interact with resources they own or are permitted to access.

---

# 120. API Security Best Practices

## Authentication

- JWT
- OAuth2
- Session Authentication

---

## Authorization

- RBAC
- Object-level permissions
- Principle of least privilege

---

## Secure Communication

- HTTPS only
- HSTS

---

## Input Validation

- Serializer validation
- Parameterized queries
- File validation

---

## Rate Limiting

- DRF throttling
- API Gateway rate limiting

---

## Secrets Management

- Environment variables
- Secret managers
- Never hard-code credentials

---

## Logging & Monitoring

- Audit logs
- Security monitoring
- Failed login tracking

---

## HTTP Security Headers

- Content-Security-Policy (CSP)
- X-Content-Type-Options
- Referrer-Policy
- X-Frame-Options

---

## Token Management

- Short-lived access tokens
- Refresh token rotation (where supported)
- Token revocation on logout if required by the architecture

---

## Dependency Management

- Keep Django and dependencies updated
- Run vulnerability scans
- Remove unused packages

---

## Interview Answer

> Secure APIs combine strong authentication, fine-grained authorization, HTTPS, secure headers, input validation, rate limiting, proper secret management, continuous monitoring, and regular dependency updates to protect against common attacks.

---

# Module Summary

- Django's authentication framework uses authentication backends, sessions, and permission checks to authenticate users.
- Sessions store authentication state on the server, while cookies store only client-side identifiers or small pieces of data.
- Django protects against common web attacks including **CSRF**, **XSS**, **SQL Injection**, and **Clickjacking** through built-in security features and secure development practices.
- CORS enables controlled cross-origin communication for browser-based applications.
- **JWT** is ideal for stateless APIs, while **Session Authentication** is commonly used for traditional web applications.
- Refresh tokens improve security by enabling short-lived access tokens.
- OAuth2 and Azure AD SSO support secure enterprise authentication and delegated authorization.
- RBAC and object-level permissions provide scalable authorization strategies.
- Production-grade API security requires HTTPS, secure headers, least-privilege authorization, input validation, rate limiting, monitoring, and careful secret management.