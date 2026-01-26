# HTTP and APIs

> *The foundation of web communication*

---

## HTTP Basics

### Request/Response Cycle

```
Client                              Server
  │                                   │
  │──── HTTP Request ────────────────▶│
  │     (Method, URL, Headers, Body)  │
  │                                   │
  │◀─── HTTP Response ────────────────│
  │     (Status, Headers, Body)       │
```

---

## HTTP Methods

| Method | Purpose | Idempotent | Safe |
|--------|---------|------------|------|
| **GET** | Retrieve resource | ✅ | ✅ |
| **POST** | Create resource | ❌ | ❌ |
| **PUT** | Update/Replace | ✅ | ❌ |
| **PATCH** | Partial update | ❌ | ❌ |
| **DELETE** | Remove resource | ✅ | ❌ |

---

## Status Codes

| Range | Category | Examples |
|-------|----------|----------|
| **2xx** | Success | 200 OK, 201 Created, 204 No Content |
| **3xx** | Redirect | 301 Moved, 304 Not Modified |
| **4xx** | Client Error | 400 Bad Request, 401 Unauthorized, 404 Not Found |
| **5xx** | Server Error | 500 Internal Error, 503 Service Unavailable |

---

## Headers

### Common Request Headers
```
Content-Type: application/json
Authorization: Bearer <token>
Accept: application/json
User-Agent: MyApp/1.0
```

### Common Response Headers
```
Content-Type: application/json
Cache-Control: max-age=3600
Set-Cookie: session=abc123
```

---

## REST Principles

1. **Stateless**: Each request contains all info needed
2. **Client-Server**: Separation of concerns
3. **Cacheable**: Responses can be cached
4. **Uniform Interface**: Consistent API design
5. **Layered System**: Middleware support

### RESTful URL Design

```
GET    /users          # List all users
GET    /users/:id      # Get single user
POST   /users          # Create user
PUT    /users/:id      # Update user
DELETE /users/:id      # Delete user

# Nested resources
GET    /users/:id/orders
POST   /users/:id/orders
```

---

## Request/Response Examples

### GET Request
```http
GET /api/users/123 HTTP/1.1
Host: api.example.com
Authorization: Bearer token123
Accept: application/json
```

### POST Request
```http
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

### Response
```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 124,
  "name": "John Doe",
  "email": "john@example.com",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

---

*Next: [[Authentication|Authentication →]]*

*Back to: [[Index|Fundamentals Home]]*
