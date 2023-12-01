import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.data.repository.PagingAndSortingRepository;

import java.util.List;

public interface UserRepository extends PagingAndSortingRepository<User, Long> {

    // Standard CRUD methods from CrudRepository

    // Additional methods for sorting
    Iterable<User> findAll(Sort sort);  // Find all users and sort them

    // Additional methods for paging
    Page<User> findAll(Pageable pageable);  // Find all users with pagination

    // Additional method for sorting and paging
    Iterable<User> findAllById(Iterable<Long> ids, Sort sort);  // Find all users by IDs and sort them

    // Additional custom query methods can be added here if needed
}




import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class User {

    @Id
    private Long id;
    private String username;
    private String email;

    // Getters and setters

    // You can generate these using your IDE or Lombok
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
-----------------------
import org.springframework.data.repository.CrudRepository;

import java.util.List;

public interface UserRepository extends CrudRepository<User, Long> {
    List<User> findByEmail(String email);

    List<User> findByUsernameAndEnabled(String username, boolean enabled);

    List<User> findByAgeGreaterThan(int age);

    List<User> findByLastNameIgnoreCase(String lastName);
}
----------------------------------------------
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserRepository userRepository;

    @GetMapping
    public List<User> getAllUsers() {
        return (List<User>) userRepository.findAll();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userRepository.findById(id).orElse(null);
    }

    @PostMapping
    public void saveUser(@RequestBody User user) {
        userRepository.save(user);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userRepository.deleteById(id);
    }
}
-------------------------------------------------
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Sort;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserRepository userRepository;

    @GetMapping
    public Iterable<User> getAllUsers() {
        return userRepository.findAll();
    }

    @GetMapping("/paged")
    public Page<User> getAllUsersPaged(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "10") int size,
            @RequestParam(defaultValue = "id") String sortBy
    ) {
        PageRequest pageRequest = PageRequest.of(page, size, Sort.by(sortBy));
        return userRepository.findAll(pageRequest);
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userRepository.findById(id).orElse(null);
    }

    @PostMapping
    public void saveUser(@RequestBody User user) {
        userRepository.save(user);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userRepository.deleteById(id);
    }
}




3.	PagingAndSortingRepository:
•	Extends CrudRepository.
•	Adds additional methods for paging and sorting data.
•	Enables sorting results by various properties.
•	Supports paginated queries using methods like findAll(Pageable pageable).
4.	JpaRepository:
•	Extends both CrudRepository and PagingAndSortingRepository.
•	Provides JPA-specific functionality on top of Crud and PagingAndSorting repositories.
•	Includes methods like flush() to trigger a flush on the persistence context, which can be useful in certain scenarios.

CrudRepository Example:
import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class User {
    @Id
    private Long id;
    private String username;
    private String email;

    // Getters and setters
}
import org.springframework.data.repository.CrudRepository;

public interface UserRepository extends CrudRepository<User, Long> {
    // Custom queries can be added here if needed
}
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public Iterable<User> getAllUsers() {
        return userRepository.findAll();
    }

    public User getUserById(Long id) {
        return userRepository.findById(id).orElse(null);
    }

    public void saveUser(User user) {
        userRepository.save(user);
    }

    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}
----------------------------------End---------------
PagingAndSortingRepository Example:
import org.springframework.data.repository.PagingAndSortingRepository;

public interface UserRepository extends PagingAndSortingRepository<User, Long> {
    // Custom queries can still be added here
}
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.stereotype.Service;

@Service
public class UserService {
    // ...

    public Page<User> getUsersWithPagination(int page, int size) {
        PageRequest pageRequest = PageRequest.of(page, size);
        return userRepository.findAll(pageRequest);
    }

    // ...
}
JPA 
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
    // Custom queries can be added here, in addition to JpaRepository methods
}
import org.springframework.stereotype.Service;

@Service
public class UserService {
    // ...

    public void flushPersistenceContext() {
        userRepository.flush();
    }

    // ...
}
