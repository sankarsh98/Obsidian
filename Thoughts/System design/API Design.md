# API Design

> Designing clean, scalable, and developer-friendly APIs.

---

## Back to [[System Design]]

---

## What is API Design?

API (Application Programming Interface) design is the process of defining how different software components communicate. Good API design makes systems easier to use, maintain, and scale.

---

## API Styles

### REST (Representational State Transfer)

Resource-oriented architecture using HTTP methods.

```
GET    /users          # List users
POST   /users          # Create user
GET    /users/123      # Get user 123
PUT    /users/123      # Update user 123
PATCH  /users/123      # Partial update user 123
DELETE /users/123      # Delete user 123
```

### GraphQL

Query language for APIs, client specifies what data it needs.

```graphql
# Query
query {
  user(id: "123") {
    name
    email
    posts {
      title
      createdAt
    }
  }
}

# Response
{
  "user": {
    "name": "John",
    "email": "john@example.com",
    "posts": [
      { "title": "Hello", "createdAt": "2024-01-15" }
    ]
  }
}
```

### gRPC

High-performance RPC framework using Protocol Buffers.

```protobuf
service UserService {
  rpc GetUser(GetUserRequest) returns (User);
  rpc CreateUser(CreateUserRequest) returns (User);
  rpc ListUsers(ListUsersRequest) returns (stream User);
}

message User {
  string id = 1;
  string name = 2;
  string email = 3;
}
```

### Comparison

| Feature | REST | GraphQL | gRPC |
|---------|------|---------|------|
| Protocol | HTTP | HTTP | HTTP/2 |
| Format | JSON/XML | JSON | Protocol Buffers |
| Flexibility | Fixed endpoints | Flexible queries | Fixed methods |
| Performance | Good | Good | Excellent |
| Browser Support | Excellent | Good | Limited |
| Learning Curve | Low | Medium | Medium |
| Best For | Web APIs | Complex queries | Microservices |

---

## REST API Design

### Resource Naming

```
Good:
/users                    # Collection
/users/123                # Single resource
/users/123/orders         # Nested resource
/users/123/orders/456     # Specific nested resource

Bad:
/getUsers                 # Don't use verbs
/user/123                 # Be consistent (plural)
/users/123/getOrders      # Verb in URL
```

### HTTP Methods

| Method | Purpose | Idempotent | Safe |
|--------|---------|------------|------|
| GET | Read resource | Yes | Yes |
| POST | Create resource | No | No |
| PUT | Replace resource | Yes | No |
| PATCH | Partial update | No | No |
| DELETE | Remove resource | Yes | No |

### HTTP Status Codes

```
2xx Success:
200 OK              - Request succeeded
201 Created         - Resource created
204 No Content      - Success, no response body

3xx Redirection:
301 Moved Permanently
304 Not Modified

4xx Client Errors:
400 Bad Request     - Invalid syntax
401 Unauthorized    - Not authenticated
403 Forbidden       - Not authorized
404 Not Found       - Resource doesn't exist
409 Conflict        - Conflict with current state
422 Unprocessable   - Validation error
429 Too Many Requests - Rate limited

5xx Server Errors:
500 Internal Error  - Server error
502 Bad Gateway     - Upstream error
503 Service Unavailable - Temporarily down
504 Gateway Timeout - Upstream timeout
```

### Request/Response Format

**Request:**
```http
POST /api/v1/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGc...

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

**Response:**
```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/v1/users/123

{
  "id": "123",
  "name": "John Doe",
  "email": "john@example.com",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

---

## Pagination

### Offset-Based

```
GET /users?offset=20&limit=10

Response:
{
  "data": [...],
  "total": 100,
  "offset": 20,
  "limit": 10
}
```

**Pros:** Simple, supports random access
**Cons:** Slow for large offsets, inconsistent with real-time data

### Cursor-Based

```
GET /users?cursor=abc123&limit=10

Response:
{
  "data": [...],
  "nextCursor": "def456",
  "prevCursor": "xyz789"
}
```

**Pros:** Consistent, efficient for large datasets
**Cons:** No random access, more complex

### Page-Based

```
GET /users?page=3&per_page=10

Response:
{
  "data": [...],
  "page": 3,
  "per_page": 10,
  "total_pages": 10,
  "total_count": 100
}
```

---

## Filtering, Sorting, and Searching

### Filtering
```
GET /users?status=active&role=admin
GET /users?created_after=2024-01-01
GET /products?price_min=100&price_max=500
```

### Sorting
```
GET /users?sort=created_at:desc
GET /users?sort=name:asc,created_at:desc
GET /products?sort=-price  # - prefix for desc
```

### Searching
```
GET /users?q=john
GET /users?search=john&search_fields=name,email
```

### Field Selection
```
GET /users?fields=id,name,email
GET /users?include=orders,profile
GET /users?exclude=password,internal_notes
```

---

## API Versioning

### URL Path Versioning
```
GET /api/v1/users
GET /api/v2/users

Most common, explicit
```

### Header Versioning
```
GET /api/users
Accept: application/vnd.api.v2+json

Cleaner URLs, less visible
```

### Query Parameter
```
GET /api/users?version=2

Easy to use, not RESTful
```

### Best Practices
```
- Use semantic versioning (v1, v2)
- Support at least 2 versions
- Deprecation notices in headers
- Clear migration documentation
```

---

## Authentication & Authorization

### API Keys
```http
GET /api/users
X-API-Key: your-api-key-here
```
**Use for:** Server-to-server, public APIs

### OAuth 2.0
```
1. User authorizes app
2. App receives authorization code
3. App exchanges code for access token
4. App uses token for API requests

GET /api/users
Authorization: Bearer access_token
```
**Use for:** User-delegated access

### JWT (JSON Web Token)
```
Header.Payload.Signature

Header: {"alg": "HS256", "typ": "JWT"}
Payload: {"sub": "123", "exp": 1234567890}
Signature: HMAC-SHA256(header + payload, secret)
```

### Auth Flow Diagram
```
+--------+     1. Login      +--------+     2. Verify    +--------+
| Client | ----------------> |  API   | ---------------> | Auth   |
+--------+                   +--------+                  | Server |
     ^                           |                       +--------+
     |                           |
     |    3. Return Token        |
     +---------------------------+
     
4. Subsequent requests include token
```

---

## Rate Limiting

### Strategies

**Fixed Window:**
```
100 requests per minute
Window: 10:00 - 10:01

At 10:00:30: 99 requests used
At 10:00:45: 1 request left
At 10:01:00: Reset to 100
```

**Sliding Window:**
```
100 requests per minute, sliding

At 10:01:30, count requests from 10:00:30 to 10:01:30
More fair than fixed window
```

**Token Bucket:**
```
Bucket capacity: 100 tokens
Refill rate: 10 tokens/second

Request uses 1 token
If bucket empty: reject request
```

### Response Headers
```http
HTTP/1.1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 45
X-RateLimit-Reset: 1642521600

HTTP/1.1 429 Too Many Requests
Retry-After: 60
```

---

## Error Handling

### Error Response Format
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request parameters",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      },
      {
        "field": "age",
        "message": "Must be at least 18"
      }
    ],
    "requestId": "req_abc123",
    "documentation": "https://api.example.com/docs/errors#VALIDATION_ERROR"
  }
}
```

### Error Codes
```
Use consistent error codes:

AUTH_001: Invalid credentials
AUTH_002: Token expired
AUTH_003: Insufficient permissions

USER_001: User not found
USER_002: Email already exists

RATE_001: Rate limit exceeded
```

---

## API Documentation

### OpenAPI/Swagger
```yaml
openapi: 3.0.0
info:
  title: User API
  version: 1.0.0

paths:
  /users:
    get:
      summary: List all users
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            default: 10
      responses:
        '200':
          description: List of users
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'

components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
        email:
          type: string
```

### Documentation Best Practices
- Interactive API explorer
- Code examples in multiple languages
- Authentication guide
- Rate limiting information
- Changelog and versioning notes

---

## API Design Best Practices

### 1. Be Consistent
```
Consistent naming:
/users, /orders, /products (all plural)

Consistent response format:
All responses follow same structure
```

### 2. Use Proper HTTP Semantics
```
GET for reading (never modify data)
POST for creating
PUT/PATCH for updating
DELETE for removing
```

### 3. Handle Errors Gracefully
```
Always return meaningful error messages
Include error codes for programmatic handling
Never expose internal errors to clients
```

### 4. Design for Forward Compatibility
```
Add optional fields, don't remove
Use versioning for breaking changes
Deprecate before removing
```

### 5. Security First
```
Always use HTTPS
Validate all input
Rate limit everything
Use proper authentication
```

---

## API Gateway Pattern

```
                    +-----------------+
                    |   API Gateway   |
                    +-----------------+
                    | - Authentication|
                    | - Rate Limiting |
                    | - Routing       |
                    | - Transformation|
                    +-----------------+
                     /       |       \
                    v        v        v
              +------+  +-------+  +--------+
              | User |  | Order |  | Product|
              | API  |  | API   |  | API    |
              +------+  +-------+  +--------+
```

---

## Related Topics
- [[Microservices]] - API design for services
- [[Load Balancing]] - API traffic distribution
- [[Caching]] - API response caching

---

## Tags
#api-design #rest #graphql #grpc #authentication #system-design
