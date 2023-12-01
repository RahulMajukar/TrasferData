CrudRepository:
Base Interface:

CrudRepository is the most basic repository interface in Spring Data.
Provides basic CRUD (Create, Read, Update, Delete) operations.
Minimal Set of Methods:

Provides a minimal set of methods such as save(), findOne(), findAll(), delete(), etc.
Limited Query Generation:

Supports basic query methods derived from method names but has limited query generation capabilities compared to JpaRepository.
JpaRepository:
Extends CrudRepository:

JpaRepository extends CrudRepository, inheriting all its basic CRUD operations.
Additional Features:

Adds additional JPA-specific features and functionality.
Provides additional methods for flushing, batch operations, and JPA-specific queries.
Query Methods:

Extends the query derivation mechanism, allowing more complex queries derived from method names.
Pagination and Sorting:

Extends PagingAndSortingRepository, introducing methods for pagination and sorting (findAll(Pageable pageable)).
Flushing:

Introduces methods for flushing the persistence context (flush()).
Batch Operations:

Supports batch operations, such as saveAll() for saving multiple entities at once.



import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {

    // Standard CRUD methods from JpaRepository

    // Additional methods for sorting
    Iterable<User> findAll(Sort sort);  // Find all users and sort them

    // Additional methods for paging
    Page<User> findAll(Pageable pageable);  // Find all users with pagination

    // Additional method for sorting and paging
    Iterable<User> findAllById(Iterable<Long> ids, Sort sort);  // Find all users by IDs and sort them

    // Additional custom query methods can be added here if needed
}


---------------------
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

    @GetMapping("/sorted")
    public Iterable<User> getAllUsersSorted(@RequestParam(defaultValue = "id") String sortBy) {
        Sort sort = Sort.by(sortBy);
        return userRepository.findAll(sort);
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

    @GetMapping("/sortedAndPaged")
    public Page<User> getAllUsersSortedAndPaged(
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

________---------------------Lambok_________________________
@Data:

Generates getters, setters, toString, equals, and hashCode methods.
java
Copy code
import lombok.Data;

@Data
public class MyClass {
    private String name;
    private int age;
}
@Getter / @Setter:

Generates getters or setters for fields.
java
Copy code
import lombok.Getter;
import lombok.Setter;

public class MyClass {
    @Getter private String name;
    @Setter private int age;
}
@NoArgsConstructor / @AllArgsConstructor / @RequiredArgsConstructor:

Generates no-argument, all-argument, or required-argument constructors.
java
Copy code
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.RequiredArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
public class MyClass {
    private String name;
    private int age;
}
@Builder:

Generates a builder pattern for the class.
java
Copy code
import lombok.Builder;

@Builder
public class MyClass {
    private String name;
    private int age;
}
@Value:

Similar to @Data, but creates an immutable class.
java
Copy code
import lombok.Value;

@Value
public class MyClass {
    private final String name;
    private final int age;
}
@Slf4j / @Log / @CommonsLog:

Integrates with various logging frameworks (SLF4J, java.util.logging, Apache Commons Logging).
java
Copy code
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class MyClass {
    public void myMethod() {
        log.debug("Debug message");
    }
}
@ToString:

Generates a toString method for the class.
java
Copy code
import lombok.ToString;

@ToString
public class MyClass {
    private String name;
    private int age;
}
@EqualsAndHashCode:

Generates equals and hashCode methods based on the fields.
java
Copy code
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class MyClass {
    private String name;
    private int age;
}
@Cleanup:

Automatically closes resources (like streams) when they go out of scope.
java
Copy code
import lombok.Cleanup;

public class MyClass {
    public void processFile() {
        @Cleanup FileInputStream fis = new FileInputStream("myfile.txt");
        // Code to read from the file
    }
}


@Getter and @Setter: Generates setter and getter methods.
@ToString: Generates toString method
@NoArgsConstructor and @AllArgsConstructor: Generates constructors that take no argument and one argument for every field.
@EqualsAndHashCode: Generates hashCode and equals implementations from the fields of your object.
@RequiredArgsConstructor: Generates one argument per final / non-null field.
@Data: A shortcut for @ToString, @EqualsAndHashCode, @Getter on all fields, and @Setter on all non-final fields and @RequiredArgsConstructor.

