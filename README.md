Project Structure
```
bash
assignment
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── example
│   │   │           ├── AssignmentApplication.java
│   │   │           ├── config
│   │   │           │   ├── SecurityConfig.java
│   │   │           │   └── SwaggerConfig.java
│   │   │           ├── controller
│   │   │           │   ├── CountryController.java
│   │   │           │   └── UserController.java
│   │   │           ├── model
│   │   │           │   ├── Country.java
│   │   │           │   └── User.java
│   │   │           ├── repository
│   │   │           │   ├── CountryRepository.java
│   │   │           │   └── UserRepository.java
│   │   │           ├── service
│   │   │           │   ├── CountryService.java
│   │   │           │   └── UserService.java
│   │   └── resources
│   │       ├── application.properties
│   │       └── static
│   └── test
│       └── java
│           └── com
│               └── example
└── Dockerfile
```

Implementation Details
*User Management (Authentication & CRUD)*
*User Entity*
```
@Entity
public class User {
  
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;
  
  private String name;
  
  private String email;
  
  private String password;
  
  // Getters and Setters
}
```

*User Repository*
```
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
  
  Optional<User> findByEmail(String email);
}
```

*User Service*
```
@Service
public class UserService {
  
  @Autowired
  private UserRepository userRepository;
  
  @Autowired
  private PasswordEncoder passwordEncoder;
  
  public User registerUser(User user) {
    user.setPassword(passwordEncoder.encode(user.getPassword()));
    return userRepository.save(user);
  }
  
  public String login(User user) {
    Optional<User> existingUser = userRepository.findByEmail(user.getEmail());
    if (existingUser.isPresent() && passwordEncoder.matches(user.getPassword(), existingUser.get().getPassword())) {
      // Generate JWT token
      return Jwts.builder().setSubject(existingUser.get().getEmail()).claim("name", existingUser.get().getName()).compact();
    }
    return null;
  }
  
  public User getUserDetails(Long id) {
    return userRepository.findById(id).orElseThrow();
  }
  
  public User updateUserDetails(Long id, User user) {
    User existingUser = getUserDetails(id);
    existingUser.setName(user.getName());
    existingUser.setEmail(user.getEmail());
    return userRepository.save(existingUser);
  }
  
  public void deleteUserAccount(Long id) {
    userRepository.deleteById(id);
  }
}
```

*User Controller*
```
@RestController
@RequestMapping("/users")
public class UserController {
  
  @Autowired
  private UserService userService;
  
  @PostMapping
  public User registerUser(@RequestBody User user) {
    return userService.registerUser(user);
  }
  
  @PostMapping("/login")
  public String login(@RequestBody User user) {
    return userService.login(user);
  }
  
  @GetMapping("/{id}")
  public User getUserDetails(@PathVariable Long id) {
    return userService.getUserDetails(id);
  }
  
  @PutMapping("/{id}")
  public User updateUserDetails(@PathVariable Long id, @RequestBody User user) {
    return userService.updateUserDetails(id, user);
  }
  
  @DeleteMapping("/{id}")
  public void deleteUserAccount(@PathVariable Long id) {
    userService.deleteUserAccount(id);
  }
}
```

*External API Integration*
*Country Entity*
```
@Entity
public class Country {
  
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;
  
  private String name;
  
  private String capital;
  
  private String region;
  
  // Getters and Setters
}
```

*Country Repository*
```
@Repository
public interface Country
```
