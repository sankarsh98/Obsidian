# Authentication System

> *Secure user authentication*

---

## Features

- User registration with validation
- Password hashing (bcrypt)
- JWT access & refresh tokens
- Protected routes middleware
- Password reset flow

---

## Token Flow

```
Login → Access Token (15min) + Refresh Token (7d)
           │
           ▼
     Access Protected Routes
           │
     Token Expired?
           │
           ▼
Use Refresh Token → New Access Token
```

---

## Implementation

```python
from datetime import datetime, timedelta
import bcrypt
import jwt

# Hash password
def hash_password(password: str) -> str:
    return bcrypt.hashpw(password.encode(), bcrypt.gensalt()).decode()

# Verify password
def verify_password(password: str, hashed: str) -> bool:
    return bcrypt.checkpw(password.encode(), hashed.encode())

# Generate tokens
def create_tokens(user_id: int):
    access = jwt.encode({
        "user_id": user_id,
        "exp": datetime.utcnow() + timedelta(minutes=15)
    }, SECRET_KEY)
    
    refresh = jwt.encode({
        "user_id": user_id,
        "exp": datetime.utcnow() + timedelta(days=7)
    }, REFRESH_SECRET)
    
    return {"access_token": access, "refresh_token": refresh}
```

---

## Endpoints

```
POST /auth/register - Create account
POST /auth/login    - Get tokens
POST /auth/refresh  - New access token
POST /auth/logout   - Invalidate tokens
GET  /auth/me       - Get current user
```

---

*Back to: [[Index|Projects Home]]*
