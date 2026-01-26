# REST API Design

> *RESTful API best practices*

---

## REST Principles

1. **Resources** - URLs represent things (nouns)
2. **HTTP Methods** - CRUD operations
3. **Stateless** - No server-side sessions
4. **Uniform Interface** - Consistent patterns

---

## URL Design

```
# Good ✓
GET    /users
GET    /users/123
POST   /users
PUT    /users/123
DELETE /users/123
GET    /users/123/orders

# Bad ✗
GET    /getUsers
POST   /createUser
GET    /user/123/getOrders
```

---

## Response Codes

| Code | Meaning | When |
|------|---------|------|
| 200 | OK | Successful GET/PUT |
| 201 | Created | Successful POST |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid input |
| 401 | Unauthorized | No auth |
| 403 | Forbidden | No permission |
| 404 | Not Found | Resource missing |
| 500 | Server Error | Bug/crash |

---

## Response Format

```json
// Success
{
  "data": {
    "id": 123,
    "name": "John"
  },
  "meta": {
    "page": 1,
    "total": 100
  }
}

// Error
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required",
    "details": [
      {"field": "email", "message": "Required"}
    ]
  }
}
```

---

## Pagination

```
GET /users?page=2&limit=20

Response:
{
  "data": [...],
  "meta": {
    "page": 2,
    "limit": 20,
    "total": 500,
    "totalPages": 25
  }
}
```

---

## Versioning

```
# URL versioning
GET /v1/users
GET /v2/users

# Header versioning
GET /users
Accept: application/vnd.api+json; version=2
```

---

*Back to: [[Index|Architecture Home]]*
