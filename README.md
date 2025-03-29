 **Project Structure:**
```
/src/main/java/com/example/demo
 ├── config
 │      ├── SecurityConfig.java
 │      ├── JwtUtil.java
 │      ├── GlobalExceptionHandler.java
 │
 ├── controller
 │      ├── AuthController.java
 │      ├── CountryController.java
 │      └── UserController.java
 │
 ├── entity
 │      ├── User.java
 │      └── Country.java
 │
 ├── repository
 │      ├── UserRepository.java
 │      └── CountryRepository.java
 │
 ├── service
 │      ├── UserService.java
 │      └── CountryService.java
 │
 ├── security
 │      ├── JwtFilter.java
 │      ├── JwtUtil.java
 │      ├── SecurityConfig.java
 │
 ├── DemoApplication.java
 ├── Dockerfile
 ├── docker-compose.yml
 ├── application.properties

/src/test/java/com/example/demo
 ├── AuthControllerTest.java
 ├── CountryControllerTest.java

/postman
 ├── collection.json
```

---

### ✅ **1. `AuthController.java` (User Authentication Controller)**
```java
package com.example.demo.controller;

import com.example.demo.entity.User;
import com.example.demo.security.JwtUtil;
import com.example.demo.service.UserService;
import org.springframework.http.ResponseEntity;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/auth")
public class AuthController {
    private final UserService userService;
    private final JwtUtil jwtUtil;
    private final AuthenticationManager authenticationManager;
    private final UserDetailsService userDetailsService;

    public AuthController(UserService userService, JwtUtil jwtUtil, AuthenticationManager authenticationManager, UserDetailsService userDetailsService) {
        this.userService = userService;
        this.jwtUtil = jwtUtil;
        this.authenticationManager = authenticationManager;
        this.userDetailsService = userDetailsService;
    }

    @PostMapping("/register")
    public ResponseEntity<User> register(@RequestBody User user) {
        return ResponseEntity.ok(userService.registerUser(user));
    }

    @PostMapping("/login")
    public ResponseEntity<String> login(@RequestBody User user) {
        authenticationManager.authenticate(new UsernamePasswordAuthenticationToken(user.getEmail(), user.getPassword()));
        UserDetails userDetails = userDetailsService.loadUserByUsername(user.getEmail());
        return ResponseEntity.ok(jwtUtil.generateToken(userDetails.getUsername()));
    }
}
```

---

### ✅ **2. `UserController.java` (User Management Controller)**
```java
package com.example.demo.controller;

import com.example.demo.entity.User;
import com.example.demo.service.UserService;
import org.springframework.http.ResponseEntity;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.web.bind.annotation.*;

import java.util.Optional;

@RestController
@RequestMapping("/users")
public class UserController {
    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/me")
    public ResponseEntity<User> getUserDetails() {
        String email = SecurityContextHolder.getContext().getAuthentication().getName();
        Optional<User> user = userService.findByEmail(email);
        return user.map(ResponseEntity::ok).orElseGet(() -> ResponseEntity.notFound().build());
    }

    @PutMapping("/update")
    public ResponseEntity<User> updateUser(@RequestBody User updatedUser) {
        return ResponseEntity.ok(userService.registerUser(updatedUser));
    }

    @DeleteMapping("/delete")
    public ResponseEntity<Void> deleteUser() {
        return ResponseEntity.noContent().build();
    }
}
```

---

### ✅ **3. `CountryController.java` (Country Management Controller)**
```java
package com.example.demo.controller;

import com.example.demo.entity.Country;
import com.example.demo.service.CountryService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/countries")
public class CountryController {
    private final CountryService countryService;

    public CountryController(CountryService countryService) {
        this.countryService = countryService;
    }

    @GetMapping
    public ResponseEntity<List<Country>> getAllCountries() {
        return ResponseEntity.ok(countryService.getAllCountries());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Country> getCountryById(@PathVariable Long id) {
        Optional<Country> country = countryService.getCountryById(id);
        return country.map(ResponseEntity::ok).orElseGet(() -> ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<Country> addCountry(@RequestBody Country country) {
        return ResponseEntity.ok(countryService.saveCountry(country));
    }

    @PutMapping("/{id}")
    public ResponseEntity<Country> updateCountry(@PathVariable Long id, @RequestBody Country country) {
        country.setId(id);
        return ResponseEntity.ok(countryService.saveCountry(country));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteCountry(@PathVariable Long id) {
        countryService.deleteCountry(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

### ✅ **Swagger API Documentation**
- Added **springdoc-openapi** in `application.properties`.
- Auto-generates API documentation at `http://localhost:8080/swagger-ui.html`.

---

### ✅ **Postman Collection**
- Provided a **Postman collection** (`/postman/collection.json`) for API testing.

---
