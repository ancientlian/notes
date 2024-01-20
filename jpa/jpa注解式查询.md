## `@Query`

```java
public interface UserRepository extends JpaRepository<User, Long> {
    @Query("select u fron User u where u.emailAddress = ?1")
    User findByEmailAddress (String enailAddress);

    @Query ("select u from User u where u.firstname 1ike :?1")
	List<User> findByFirstnameEndswith(String firstname);

	@Query (value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1"， nativeQuery=true)
	User findByEmailAddress (String emailAddress);

}
```

### 排序

```java
public interface UserRepository extends JpaRepository<User, Long>{
	@Query("select u fron User u where u.lastname 1ike ?1%")
	List<User> findByAndSort (String lastname, Sort sort)；

	@Query ("select u. id, LENGTH (u.firstname) as fn_len from User u where u.lastname like ?1%")
	List<object []> findByAsArrayAndSort (String lastname, Sort sort)；
}

//调用方的写法，如下：
repo. findByAndSort ("lannister", new Sort ("firstname"));
repo. findByAndSort ("stark", new Sort ("LENGTH (firstname)"));
repo. findByAndSort ("targaryen", JpaSort.unsafe ("LENGTH (firstname)"));
repo. findByAsArrayAndSort ("bolton", new Sort ("fn_len"));
```

### 分页

直接用 Page 对象接收接口，参数直接用 Pageable 的实现类即可

```java
public interface UserRepository extends JpaRepository<User, Long>{

	@Query (value = "select u from User u where u.lastname = ?1")
	Page<User> findByLastname (String lastname, Pageable pageable)；
}

//调用者的写法
repository.findByFirstName("jackzhang", new PageRequest(1, 10));
```

## SpEL 表达式

对象传参

```java
@Query("select u from User u where u.firstname = :#{#customer.firstname}")
List<User> findUsersByCustomersFirstname(@Param("customer") Customer customer);
```

## @Modifying 修改查询

在@Modifying 注解实现只需要参数绑定的 update 查询执行
简单地针对某些特定属性的更新也可以直接用基类里面提供的通用 save。还有第三种方法，就是自定义 Repository，使用 EntityManager 来进行更新操作。用法如下：

```java
@Modifying
@Query ("update User u set u . firstname = ?1 where u . lastname = ？2")
int setFixedFirstnameFor (String firstname, String lastname);

interface UserRepository extends Repository<user, Long>{
	void deleteByRoleld (long roleld);

	@Modifying
	@Query ("delete from User u where user.role.id = ?1"）
	void deleteInBulkByRoleId (long roleId);
}

```

## @QueryHints

略

## @Procedure 储存过程的查询方法

略

## @NamedQueries 预定义查询

## 参考

[Spring Data Jpa (四)注解式查询方法 - youqc - 博客园](https://www.cnblogs.com/youqc/p/11097574.html)
