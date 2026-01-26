# Microservices Project

> *Distributed service architecture*

---

## Project Overview

Build an e-commerce system with microservices.

---

## Services

```
┌─────────────┐   ┌─────────────┐   ┌─────────────┐
│    Users    │   │  Products   │   │   Orders    │
│   Service   │   │   Service   │   │   Service   │
└──────┬──────┘   └──────┬──────┘   └──────┬──────┘
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
              ┌──────────▼──────────┐
              │    API Gateway      │
              └─────────────────────┘
```

---

## Tech Stack

- **Gateway**: Kong / Nginx
- **Services**: FastAPI / Express
- **Communication**: REST + RabbitMQ
- **Database**: PostgreSQL per service
- **Containers**: Docker + Docker Compose

---

## Docker Compose

```yaml
version: '3.8'

services:
  gateway:
    image: kong:latest
    ports:
      - "8000:8000"
  
  users:
    build: ./users-service
    environment:
      - DATABASE_URL=postgres://...
  
  products:
    build: ./products-service
    environment:
      - DATABASE_URL=postgres://...
  
  orders:
    build: ./orders-service
    environment:
      - DATABASE_URL=postgres://...
  
  rabbitmq:
    image: rabbitmq:3-management
    ports:
      - "5672:5672"
```

---

*Back to: [[Index|Projects Home]]*
