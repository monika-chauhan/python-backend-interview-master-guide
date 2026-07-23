# Django Interview Master Guide

# Module 11: Production Django (Questions 161–180)

---

# 161. Deploy Django Using Gunicorn

## What is Gunicorn?

**Gunicorn (Green Unicorn)** is a production-grade **WSGI HTTP server** for Python applications. It runs Django applications and serves requests from a reverse proxy like Nginx.

It is lightweight, stable, and widely used in Linux production environments.

---

## Deployment Architecture

```
Client
   │
   ▼
Nginx
   │
   ▼
Gunicorn
   │
   ▼
Django (WSGI)
   │
   ▼
PostgreSQL
```

---

## Installation

```bash
pip install gunicorn
```

---

## Run Gunicorn

```bash
gunicorn myproject.wsgi:application \
    --workers 4 \
    --bind 0.0.0.0:8000
```

---

## Worker Count

A common starting point:

```
Workers = (2 × CPU Cores) + 1
```

Tune based on workload and benchmarking.

---

## Advantages

- Fast
- Stable
- Production ready
- Easy integration with Nginx
- Supports multiple worker types

---

## Interview Answer

> Gunicorn is a production-ready WSGI server that executes Django applications and is commonly deployed behind Nginx for high-performance request handling.

---

# 162. Gunicorn vs uWSGI

| Feature | Gunicorn | uWSGI |
|----------|-----------|--------|
| Setup | Easy | Complex |
| Configuration | Simple | Extensive |
| Performance | Excellent | Excellent |
| Learning Curve | Low | High |
| Features | Basic | Very Rich |
| Popularity | Very High | High |

---

## When to Use

### Gunicorn

- Most Django deployments
- Docker
- Kubernetes
- Simpler configuration

### uWSGI

- Highly customized deployments
- Advanced tuning requirements

---

## Interview Answer

> Gunicorn is simpler to configure and widely used for Django deployments, while uWSGI offers more advanced features and tuning options at the cost of increased complexity.

---

# 163. Nginx + Gunicorn Architecture

## Architecture

```
Internet

↓

Nginx

↓

Gunicorn

↓

Django

↓

Redis

↓

PostgreSQL
```

---

## Responsibilities

### Nginx

- Reverse proxy
- SSL termination
- Static files
- Load balancing
- Compression

### Gunicorn

- Run Django application
- Manage worker processes

---

## Why Not Expose Gunicorn Directly?

Nginx provides:

- Better performance
- Security
- Static file serving
- Request buffering
- TLS termination

---

## Interview Answer

> Nginx acts as a reverse proxy, handling SSL, static assets, and load balancing, while Gunicorn runs Django worker processes that execute application code.

---

# 164. Dockerizing Django

## Why Docker?

Docker packages the application and its dependencies into a portable container.

---

## Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["gunicorn", "myproject.wsgi:application"]
```

---

## Benefits

- Consistent environments
- Easy deployment
- Isolation
- Portability
- Scalability

---

## Production Stack

```
Docker

↓

Gunicorn

↓

Django

↓

Redis

↓

PostgreSQL
```

---

## Interview Answer

> Docker packages Django with all dependencies into an isolated container, ensuring consistent deployments across development, testing, and production environments.

---

# 165. Django on Kubernetes

## Architecture

```
Ingress

↓

Service

↓

Deployment

↓

Pods

↓

Gunicorn

↓

Django
```

---

## Kubernetes Features

- Auto scaling
- Self healing
- Rolling updates
- Service discovery
- Health checks
- Resource limits

---

## Common Resources

- Deployment
- Service
- ConfigMap
- Secret
- Ingress
- HorizontalPodAutoscaler

---

## Interview Answer

> Kubernetes manages containerized Django applications by providing scaling, self-healing, rolling updates, service discovery, and resource management.

---

# 166. Django on AWS

## Common AWS Services

| Service | Purpose |
|----------|----------|
| EC2 | Compute |
| ECS / EKS | Containers |
| RDS | PostgreSQL/MySQL |
| ElastiCache | Redis |
| S3 | Static & Media Files |
| CloudFront | CDN |
| ALB | Load Balancer |
| Route 53 | DNS |

---

## Typical Architecture

```
Route53

↓

Load Balancer

↓

EC2 / ECS / EKS

↓

Gunicorn

↓

Django

↓

RDS

↓

Redis
```

---

## Interview Answer

> Django applications on AWS commonly use EC2 or containers for compute, RDS for databases, ElastiCache for Redis, S3 for storage, CloudFront as a CDN, and an Application Load Balancer for traffic distribution.

---

# 167. Django on Azure

## Common Azure Services

- Azure App Service
- Azure Kubernetes Service (AKS)
- Azure Database for PostgreSQL
- Azure Cache for Redis
- Azure Blob Storage
- Azure Key Vault
- Azure Monitor

---

## Architecture

```
Azure App Gateway

↓

AKS / App Service

↓

Django

↓

Azure PostgreSQL

↓

Redis
```

---

## Interview Answer

> Azure deployments typically combine App Service or AKS with managed PostgreSQL, Redis, Blob Storage, Key Vault, and Azure Monitor to build scalable Django applications.

---

# 168. Django on GCP

## Common GCP Services

- Cloud Run
- Google Kubernetes Engine (GKE)
- Compute Engine
- Cloud SQL
- Memorystore (Redis)
- Cloud Storage
- Cloud Load Balancer

---

## Architecture

```
Cloud Load Balancer

↓

Cloud Run / GKE

↓

Django

↓

Cloud SQL

↓

Memorystore
```

---

## Interview Answer

> On GCP, Django is commonly deployed to Cloud Run or GKE with Cloud SQL for relational databases, Memorystore for Redis, and Cloud Storage for static and media files.

---

# 169. Environment Variables

## Why?

Never hard-code configuration values.

---

## Example

```python
import os

SECRET_KEY = os.environ["SECRET_KEY"]
```

---

## Typical Variables

- SECRET_KEY
- DATABASE_URL
- REDIS_URL
- DEBUG
- ALLOWED_HOSTS
- API keys

---

## Benefits

- Security
- Environment-specific configuration
- Twelve-Factor App compliance

---

## Interview Answer

> Environment variables separate configuration from code, improving security, portability, and deployment flexibility across environments.

---

# 170. Secrets Management

## What are Secrets?

Sensitive information such as:

- Database passwords
- API keys
- JWT secrets
- OAuth credentials
- Encryption keys

---

## Secret Managers

- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager
- HashiCorp Vault

---

## Never Store

```python
SECRET_KEY = "abcd1234"
```

inside source code.

---

## Interview Answer

> Secrets should be stored in dedicated secret management systems or secure environment variables rather than in source code or version control.

---

# 171. Health Check APIs

## Purpose

Allow load balancers and orchestrators to verify application health.

---

## Example

```python
from django.http import JsonResponse

def health(request):

    return JsonResponse({
        "status": "healthy"
    })
```

---

## Types

### Liveness Probe

"Is the process alive?"

### Readiness Probe

"Can it serve traffic?"

---

## Kubernetes Example

```
/health/live

/health/ready
```

---

## Interview Answer

> Health check APIs expose endpoints used by load balancers and orchestration platforms to determine whether an application is alive and ready to receive traffic.

---

# 172. Logging

## Purpose

Capture application events for debugging, auditing, and monitoring.

---

## Django Logging

```python
import logging

logger = logging.getLogger(__name__)

logger.info("User logged in")
```

---

## Best Practices

- Structured logging (e.g., JSON)
- Include request IDs
- Log exceptions with stack traces
- Avoid logging secrets
- Use log rotation and centralized aggregation

---

## Interview Answer

> Logging records application events and errors, enabling troubleshooting, auditing, and operational monitoring. Production systems should use structured logs and centralized log aggregation.

---

# 173. Monitoring

## What to Monitor?

- CPU
- Memory
- Response time
- Error rate
- Database latency
- Cache hit ratio
- Queue length

---

## Common Tools

- Prometheus
- Grafana
- Datadog
- New Relic
- Azure Monitor
- CloudWatch

---

## Interview Answer

> Monitoring provides visibility into application health and performance by collecting metrics, logs, and traces to detect issues proactively.

---

# 174. Prometheus

## Definition

Prometheus is an open-source metrics collection and monitoring system.

---

## Architecture

```
Application

↓

/metrics

↓

Prometheus

↓

Grafana
```

---

## Metrics Examples

- HTTP requests
- Response latency
- CPU
- Memory
- Database queries

---

## Interview Answer

> Prometheus periodically scrapes metrics from applications and infrastructure, storing time-series data that can be queried for monitoring and alerting.

---

# 175. Grafana

## Definition

Grafana is a visualization platform for monitoring data.

---

## Dashboards

- CPU
- Memory
- API latency
- Error rate
- Cache hit ratio
- Database performance

---

## Flow

```
Application

↓

Prometheus

↓

Grafana Dashboard
```

---

## Interview Answer

> Grafana visualizes metrics from Prometheus and other data sources through dashboards and supports alerting for operational monitoring.

---

# 176. Sentry

## Definition

Sentry is an error tracking and application monitoring platform.

---

## Example

```
Exception

↓

Sentry SDK

↓

Dashboard

↓

Stack Trace

↓

Alert
```

---

## Benefits

- Error grouping
- Stack traces
- Release tracking
- Performance monitoring

---

## Interview Answer

> Sentry captures exceptions and performance data, helping developers identify, prioritize, and resolve production issues quickly.

---

# 177. Graceful Shutdown

## Definition

Graceful shutdown allows an application to finish in-flight requests before stopping.

---

## Flow

```
SIGTERM

↓

Stop Accepting Requests

↓

Finish Active Requests

↓

Close Connections

↓

Exit
```

---

## Importance

- Prevents request loss
- Avoids data corruption
- Supports rolling deployments

---

## Interview Answer

> Graceful shutdown ensures the application completes active requests and releases resources cleanly before terminating.

---

# 178. Rolling Deployment

## Definition

Rolling deployment updates application instances gradually without taking the entire system offline.

---

## Flow

```
Old Pods

↓

Replace One

↓

Health Check

↓

Replace Next

↓

Complete
```

---

## Advantages

- Minimal downtime
- Easy rollback
- Controlled rollout

---

## Interview Answer

> Rolling deployment updates instances incrementally, maintaining application availability throughout the deployment process.

---

# 179. Blue-Green Deployment

## Definition

Maintain two identical production environments.

---

## Architecture

```
Blue Environment (Live)

Green Environment (New)

↓

Switch Traffic

↓

Green Becomes Live
```

---

## Benefits

- Near-zero downtime
- Fast rollback
- Safe releases

---

## Drawbacks

- Higher infrastructure cost
- Duplicate environments

---

## Interview Answer

> Blue-Green deployment uses two identical production environments, allowing traffic to switch instantly to the new version and enabling rapid rollback if necessary.

---

# 180. Zero Downtime Deployment

## Definition

Deploy new versions without interrupting user traffic.

---

## Techniques

- Rolling deployments
- Blue-Green deployments
- Canary releases
- Graceful shutdown
- Backward-compatible database migrations
- Health checks
- Load balancers

---

## Enterprise Deployment Flow

```
Build

↓

Automated Tests

↓

Docker Image

↓

CI/CD Pipeline

↓

Kubernetes

↓

Rolling Update

↓

Health Checks

↓

Traffic Shift

↓

Monitoring

↓

Deployment Complete
```

---

## Best Practices

- Avoid breaking database changes
- Use feature flags for gradual rollout
- Monitor key metrics during deployment
- Automate rollback on failures
- Ensure application compatibility across versions

---

## Interview Answer

> Zero-downtime deployment combines load balancers, health checks, rolling or Blue-Green deployments, graceful shutdown, and backward-compatible database migrations to release new versions without disrupting users.

---

# Module Summary

- Gunicorn is the most commonly used production WSGI server for Django and is typically deployed behind Nginx.
- Nginx handles SSL termination, static files, compression, and reverse proxying, while Gunicorn executes Django application code.
- Docker simplifies packaging and deployment, and Kubernetes provides orchestration, scaling, and self-healing.
- Django can be deployed on AWS, Azure, and GCP using managed services for compute, databases, caching, storage, and load balancing.
- Configuration should be managed using environment variables, while sensitive credentials belong in dedicated secret management systems.
- Health check endpoints enable load balancers and orchestrators to determine application readiness and liveness.
- Production systems require centralized logging, monitoring, metrics collection with Prometheus, visualization with Grafana, and error tracking through Sentry.
- Graceful shutdown prevents request loss during deployments, while Rolling, Blue-Green, and Zero-Downtime deployment strategies ensure continuous application availability.