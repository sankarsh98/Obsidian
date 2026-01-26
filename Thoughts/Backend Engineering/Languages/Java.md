# Java

> *Enterprise-grade backend development*

---

## Why Java?

- **Enterprise standard** - Used by large companies
- **Strong typing** - Catches errors early
- **JVM ecosystem** - Mature, well-tested
- **Job market** - High demand

---

## Popular Frameworks

| Framework | Type | Best For |
|-----------|------|----------|
| **Spring Boot** | Full-featured | Enterprise apps |
| **Micronaut** | Cloud-native | Microservices |
| **Quarkus** | Kubernetes | Cloud/serverless |

---

## Spring Boot Example

```java
// UserController.java
@RestController
@RequestMapping("/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        return userService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }
    
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public User createUser(@RequestBody @Valid UserDto userDto) {
        return userService.create(userDto);
    }
}

// UserService.java
@Service
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public List<User> findAll() {
        return userRepository.findAll();
    }
    
    public Optional<User> findById(Long id) {
        return userRepository.findById(id);
    }
    
    public User create(UserDto dto) {
        User user = new User(dto.getName(), dto.getEmail());
        return userRepository.save(user);
    }
}
```

---

## JPA Entity

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false)
    private String name;
    
    @Column(unique = true)
    private String email;
    
    @CreatedDate
    private LocalDateTime createdAt;
}
```

---

## Project Structure

```
src/main/java/com/example/
├── controller/
├── service/
├── repository/
├── model/
├── dto/
└── Application.java
```

---

*Back to: [[Index|Languages Home]]*
