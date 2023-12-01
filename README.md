# TrasferData
1.	CrudRepository:
•	Basic CRUD operations: Create, Read, Update, Delete.
•	Includes methods like findOne(), findAll(), save(), delete(), etc.
•	It's a generic interface, allowing you to define the type of entity it manages.

The CrudRepository interface in Spring Data JPA provides a set of standard CRUD (Create, Read, Update, Delete) methods. Here are some of the key methods available in the CrudRepository:

Save:

save(S entity): Saves the given entity. It can be used for both insert and update operations.
Retrieve:

findById(ID id): Retrieves an entity by its id.
findAll(): Returns all entities.
findAllById(Iterable<ID> ids): Returns all entities identified by the given ids.
Delete:

deleteById(ID id): Deletes the entity with the given id.
delete(T entity): Deletes the given entity.
deleteAll(): Deletes all entities.
Existence Check:

existsById(ID id): Checks if an entity with the given id exists.
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
