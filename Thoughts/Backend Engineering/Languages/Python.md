# Python for Backend

> *The most beginner-friendly backend language*

---

## Why Python?

- **Easy to learn** - Clean, readable syntax
- **Huge ecosystem** - Libraries for everything
- **Versatile** - Web, ML, automation, data
- **Active community** - Great documentation

---

## Popular Frameworks

| Framework | Type | Best For |
|-----------|------|----------|
| **FastAPI** | Modern async | APIs, microservices |
| **Django** | Batteries-included | Full web apps |
| **Flask** | Minimal | Simple APIs, learning |

---

## FastAPI Example

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class User(BaseModel):
    name: str
    email: str

users = {}

@app.get("/users")
def list_users():
    return list(users.values())

@app.get("/users/{user_id}")
def get_user(user_id: int):
    if user_id not in users:
        raise HTTPException(status_code=404, detail="User not found")
    return users[user_id]

@app.post("/users")
def create_user(user: User):
    user_id = len(users) + 1
    users[user_id] = {"id": user_id, **user.dict()}
    return users[user_id]
```

---

## Django Example

```python
# models.py
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)

# views.py
from rest_framework import viewsets
from .models import User
from .serializers import UserSerializer

class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer
```

---

## Flask Example

```python
from flask import Flask, jsonify, request

app = Flask(__name__)
users = []

@app.route('/users', methods=['GET'])
def get_users():
    return jsonify(users)

@app.route('/users', methods=['POST'])
def create_user():
    user = request.json
    user['id'] = len(users) + 1
    users.append(user)
    return jsonify(user), 201

if __name__ == '__main__':
    app.run(debug=True)
```

---

## Essential Libraries

| Library | Purpose |
|---------|---------|
| SQLAlchemy | ORM |
| Pydantic | Data validation |
| pytest | Testing |
| Celery | Background tasks |
| Redis-py | Caching |

---

*Back to: [[Index|Languages Home]]*
