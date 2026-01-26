# Go

> *Simple, fast, and concurrent*

---

## Why Go?

- **Performance** - Compiled, fast execution
- **Simplicity** - Small language, easy to learn
- **Concurrency** - Goroutines are lightweight
- **Deployment** - Single binary, no dependencies

---

## Popular Frameworks

| Framework | Type | Best For |
|-----------|------|----------|
| **Gin** | Fast | High-performance APIs |
| **Echo** | Minimal | Simple APIs |
| **Fiber** | Express-like | Express developers |

---

## Gin Example

```go
package main

import (
    "net/http"
    "github.com/gin-gonic/gin"
)

type User struct {
    ID    int    `json:"id"`
    Name  string `json:"name"`
    Email string `json:"email"`
}

var users []User

func main() {
    r := gin.Default()
    
    r.GET("/users", func(c *gin.Context) {
        c.JSON(http.StatusOK, users)
    })
    
    r.GET("/users/:id", func(c *gin.Context) {
        id := c.Param("id")
        for _, user := range users {
            if fmt.Sprintf("%d", user.ID) == id {
                c.JSON(http.StatusOK, user)
                return
            }
        }
        c.JSON(http.StatusNotFound, gin.H{"error": "not found"})
    })
    
    r.POST("/users", func(c *gin.Context) {
        var user User
        if err := c.ShouldBindJSON(&user); err != nil {
            c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            return
        }
        user.ID = len(users) + 1
        users = append(users, user)
        c.JSON(http.StatusCreated, user)
    })
    
    r.Run(":8080")
}
```

---

## Goroutines

```go
// Concurrent execution
func main() {
    // Start goroutine
    go fetchData("https://api1.example.com")
    go fetchData("https://api2.example.com")
    
    // Wait for completion
    time.Sleep(time.Second)
}

// Channels for communication
func worker(jobs <-chan int, results chan<- int) {
    for job := range jobs {
        results <- job * 2
    }
}
```

---

## Project Structure

```
myapp/
├── cmd/
│   └── server/
│       └── main.go
├── internal/
│   ├── handlers/
│   ├── models/
│   └── repository/
├── pkg/
├── go.mod
└── go.sum
```

---

*Back to: [[Index|Languages Home]]*
