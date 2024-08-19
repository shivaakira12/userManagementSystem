
# User Management System

This is a simple user management system built using Java and Spring Boot. The system allows you to perform basic CRUD (Create, Read, Update, Delete) operations on users.

## Prerequisites

- Java 11 or higher
- MySQL database
- Maven

## Setup

1. **Clone the repository**:

   ```bash
   git clone https://github.com/yourusername/usermanagement.git
   cd usermanagement


2. **Configure the database connection** in `application.properties`:

   ```properties
   spring.datasource.url=jdbc:mysql://localhost:3306/usermanagementsystem
   spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
   spring.datasource.username=root
   spring.datasource.password=Adventure@25
   spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
   spring.jpa.hibernate.ddl-auto=update
   ```

3. **Build the project**:

   ```bash
   mvn clean install
   ```

4. **Run the application**:

   ```bash
   mvn spring-boot:run
   ```

## API Endpoints

The application exposes the following RESTful API endpoints:

- **Save User**: `POST /api/users/save`
  
  Example:
  
  ```json
  {
    "name": "John Doe",
    "email": "johndoe@example.com",
    "password": "password123",
    "address": "123 Main St"
  }
  ```

- **Get All Users**: `GET /api/users/getUsers`

- **Update User**: `PUT /api/users/update`
  
  Example:
  
  ```json
  {
    "id": 1,
    "name": "John Doe",
    "email": "johndoe@example.com",
    "password": "newpassword123",
    "address": "456 Elm St"
  }
  ```

- **Delete User**: `DELETE /api/users/delete/{id}`

- **Get User by ID**: `GET /api/users/user/{id}`

## Project Structure

```plaintext
src/
├── main/
│   ├── java/
│   │   └── com/
│   │       └── example/
│   │           ├── controller/
│   │           │   └── UserController.java
│   │           ├── model/
│   │           │   └── User.java
│   │           ├── repository/
│   │           │   └── UserRepository.java
│   │           ├── service/
│   │           │   ├── UserService.java
│   │           │   └── UserServiceImplementation.java
│   └── resources/
│       └── application.properties
└── test/
    └── java/
        └── com/
            └── example/
                └── UserManagementSystemApplicationTests.java
```

## Code Overview

### 1. **Entity Class (`User`)**

```java
package com.example.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name="User")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    private String name;
    private String email;
    private String password;
    private String address;

    // Getters and Setters
    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    public User() {
        super();
    }

    public User(Integer id, String name, String email, String password, String address) {
        super();
        this.id = id;
        this.name = name;
        this.email = email;
        this.password = password;
        this.address = address;
    }
}
```

### 2. **Controller Class (`UserController`)**

```java
package com.example.controller;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.util.ObjectUtils;
import org.springframework.web.bind.annotation.*;

import com.example.model.User;
import com.example.service.UserService;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping("/save")
    public ResponseEntity<?> saveUser(@RequestBody User user) {
        User saveUser = userService.saveUser(user);

        if (ObjectUtils.isEmpty(saveUser)) {
            return new ResponseEntity<>("User not saved", HttpStatus.INTERNAL_SERVER_ERROR);
        }
        return new ResponseEntity<>(saveUser, HttpStatus.CREATED);
    }

    @GetMapping("/getUsers")
    public ResponseEntity<?> getAllUser() {
        List<User> allUsers = userService.getAllUser();
        return new ResponseEntity<>(allUsers, HttpStatus.OK);
    }

    @PutMapping("/update")
    public ResponseEntity<?> updateUser(@RequestBody User user) {
        User saveUser = userService.saveUser(user);

        if (ObjectUtils.isEmpty(saveUser)) {
            return new ResponseEntity<>("User not updated", HttpStatus.INTERNAL_SERVER_ERROR);
        }
        return new ResponseEntity<>(saveUser, HttpStatus.OK);
    }

    @DeleteMapping("/delete/{id}")
    public ResponseEntity<?> deleteUser(@PathVariable Integer id) {
        userService.deleteUser(id);
        return new ResponseEntity<>("Delete Successfully", HttpStatus.OK);
    }

    @GetMapping("/user/{id}")
    public ResponseEntity<?> getUserById(@PathVariable Integer id) {
        User userById = userService.getUserById(id);
        if (ObjectUtils.isEmpty(userById)) {
            return new ResponseEntity<>("User not found", HttpStatus.NOT_FOUND);
        }
        return new ResponseEntity<>(userById, HttpStatus.OK);
    }
}
```

### 3. **Service Layer (`UserService` and `UserServiceImplementation`)**

#### **UserService Interface**

```java
package com.example.service;

import java.util.List;
import com.example.model.User;

public interface UserService {
    public User saveUser(User user);
    public List<User> getAllUser();
    public User updateUser(User user);
    public void deleteUser(Integer userId);
    public User getUserById(Integer userId);
}
```

#### **UserServiceImplementation Class**

```java
package com.example.service;

import java.util.List;
import java.util.Optional;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.example.model.User;
import com.example.repository.UserRepository;

@Service
public class UserServiceImplementation implements UserService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public User saveUser(User user) {
        User savedUser = userRepository.save(user);
        return savedUser;
    }

    @Override
    public List<User> getAllUser() {
        List<User> allUsers = userRepository.findAll();
        return allUsers;
    }

    @Override
    public User updateUser(User user) {
        return userRepository.save(user);
    }

    @Override
    public void deleteUser(Integer userId) {
        Optional<User> findByIdUser = userRepository.findById(userId);

        if (findByIdUser.isPresent()) {
            User user = findByIdUser.get();
            userRepository.delete(user);
        }
    }

    @Override
    public User getUserById(Integer userId) {
        Optional<User> findByIdUser = userRepository.findById(userId);
        if (findByIdUser.isPresent()) {
            return findByIdUser.get();
        }
        return null;
    }
}
```

### 4. **Repository Interface (`UserRepository`)**

```java
package com.example.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import com.example.model.User;

public interface UserRepository extends JpaRepository<User, Integer> {

}
```

### 5. **Application Properties**

```properties
spring.application.name=ProductManagementSystem

spring.datasource.url=jdbc:mysql://localhost:3306/productmanagementsystem
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=Adventure@25
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
spring.jpa.hibernate.ddl-auto=update
```

## License

This project is licensed under the MIT License.
```

MIT License

Copyright (c) [2024] [Shiva]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
