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

