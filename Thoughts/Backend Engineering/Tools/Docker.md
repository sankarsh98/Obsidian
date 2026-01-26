# Docker

> *Containerization for consistent deployments*

---

## Why Docker?

- **Consistency** - Same environment everywhere
- **Isolation** - No conflicts between apps
- **Portability** - Runs on any system
- **Efficiency** - Lighter than VMs

---

## Key Concepts

```
┌─────────────────────────────────────┐
│              Host OS                │
├─────────────────────────────────────┤
│           Docker Engine             │
├───────────┬───────────┬─────────────┤
│Container 1│Container 2│ Container 3 │
│  (App A)  │  (App B)  │   (DB)      │
└───────────┴───────────┴─────────────┘
```

---

## Dockerfile

```dockerfile
# Base image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Copy dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy application
COPY . .

# Expose port
EXPOSE 8000

# Run command
CMD ["python", "main.py"]
```

---

## Commands

```bash
# Build image
docker build -t myapp:latest .

# Run container
docker run -p 8000:8000 myapp:latest

# Run with environment
docker run -e DATABASE_URL=postgres://... myapp

# List containers
docker ps

# Stop container
docker stop <container_id>

# View logs
docker logs <container_id>
```

---

## Docker Compose

```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgres://user:pass@db/mydb
    depends_on:
      - db
  
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

```bash
docker-compose up -d
docker-compose down
```

---

*Back to: [[Index|Tools Home]]*
