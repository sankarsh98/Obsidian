# Authentication & Security

> *Securing your applications*

---

## Authentication vs Authorization

| Concept | Question | Example |
|---------|----------|---------|
| **Authentication** | Who are you? | Login with username/password |
| **Authorization** | What can you do? | Admin vs regular user |

---

## Session-Based Authentication

```
┌────────┐                         ┌────────┐
│ Client │                         │ Server │
└────┬───┘                         └────┬───┘
     │ POST /login                      │
     │ {email, password}                │
     │─────────────────────────────────▶│
     │                                  │ Create session
     │    Set-Cookie: sessionId=abc     │ Store in DB/Redis
     │◀─────────────────────────────────│
     │                                  │
     │ GET /profile                     │
     │ Cookie: sessionId=abc            │
     │─────────────────────────────────▶│
     │                                  │ Validate session
     │    {user data}                   │
     │◀─────────────────────────────────│
```

---

## JWT (JSON Web Token)

### Structure
```
header.payload.signature

eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxMjN9.abc123signature
```

### Flow
```
┌────────┐                         ┌────────┐
│ Client │                         │ Server │
└────┬───┘                         └────┬───┘
     │ POST /login                      │
     │─────────────────────────────────▶│
     │                                  │ Generate JWT
     │    {token: "eyJ..."}            │
     │◀─────────────────────────────────│
     │                                  │
     │ GET /profile                     │
     │ Authorization: Bearer eyJ...     │
     │─────────────────────────────────▶│
     │                                  │ Verify JWT
     │    {user data}                   │
     │◀─────────────────────────────────│
```

### JWT Example (Node.js)
```javascript
const jwt = require('jsonwebtoken');

// Generate
const token = jwt.sign(
  { userId: 123 },
  process.env.JWT_SECRET,
  { expiresIn: '24h' }
);

// Verify
const decoded = jwt.verify(token, process.env.JWT_SECRET);
```

---

## OAuth 2.0

### Common Flows

| Flow | Use Case |
|------|----------|
| Authorization Code | Web apps (most secure) |
| PKCE | Mobile/SPA apps |
| Client Credentials | Server-to-server |
| Refresh Token | Long-lived access |

### Authorization Code Flow
```
User → App → Auth Server → User Login → Redirect with Code → 
App → Exchange Code for Token → Access API
```

---

## Password Security

### DO ✅
```python
import bcrypt

# Hash password
hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt())

# Verify
bcrypt.checkpw(password.encode(), hashed)
```

### DON'T ❌
- Store plain text passwords
- Use MD5 or SHA1 alone
- Use same salt for all users

---

## Security Best Practices

| Practice | Implementation |
|----------|----------------|
| **HTTPS** | Always use TLS |
| **Rate Limiting** | Prevent brute force |
| **Input Validation** | Prevent injection |
| **CORS** | Restrict origins |
| **Helmet.js** | Security headers |
| **SQL Parameterization** | Prevent SQL injection |

---

*Next: [[System Design|System Design →]]*

*Back to: [[Index|Fundamentals Home]]*
