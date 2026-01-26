# Build a REST API

> *Your first backend project*

---

## Project Overview

Build a complete REST API for a task management app.

---

## Features

- CRUD for tasks
- User authentication
- Input validation
- Error handling
- Database persistence

---

## Tech Stack

- **Framework**: FastAPI / Express / Gin
- **Database**: PostgreSQL
- **ORM**: SQLAlchemy / Prisma / GORM

---

## API Endpoints

```
POST   /auth/register
POST   /auth/login

GET    /tasks
GET    /tasks/:id
POST   /tasks
PUT    /tasks/:id
DELETE /tasks/:id
```

---

## Implementation Steps

1. **Setup project** with framework
2. **Design database** schema
3. **Implement auth** (JWT)
4. **Build CRUD** endpoints
5. **Add validation** and error handling
6. **Write tests**
7. **Document API** (Swagger/OpenAPI)
8. **Deploy** (Railway/Render/Fly.io)

---

## Example Code (FastAPI)

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class TaskCreate(BaseModel):
    title: str
    description: str = None

@app.post("/tasks")
def create_task(task: TaskCreate):
    new_task = db.create(Task(**task.dict()))
    return new_task

@app.get("/tasks/{task_id}")
def get_task(task_id: int):
    task = db.get(Task, task_id)
    if not task:
        raise HTTPException(status_code=404, detail="Not found")
    return task
```

---

*Back to: [[Index|Projects Home]]*
