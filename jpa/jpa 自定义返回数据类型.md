# spring-data-jpa 使用不同形式的返回类型

## 使用 List<Object[]>

```java
@Query("select p.firstname, p.lastname, p.name from Person p where p.name = ?1 order by p.id")
List<Object[]> findAllByName4(String name);


List<Object[]> byName4 = personRepository.findAllByName4("poi");
// 想找到对应的 key 就需要数对应的key
for (Object[] objects : byName4) {
    System.out.println("objects = " + Arrays.toString(objects));
}

```

## 使用 `List<Map<String, Object>>`

```java
@Query("select p.firstname as firstname, p.lastname as lastname, p.name as name from Person p where p.name = ?1 order by p.id")
List<Map<String, Object>> findAllByName3(String name);
```

需要写 JPQL 语句，必须用 as 取别名，否则就没有 key 值
优点是不用构造 DTO 直接输出给前端
缺点是查出来的不是对象，不方便再处理业务逻辑，若参数很多，就会很繁琐

## 使用 Projections（interface 接收）

在使用`spring-data-jpa`的`2.1.0.RELEASE`及之后的版本都是可以正常的使用 interface 来进行映射自定义接受参数。
官方文档：<https://docs.spring.io/spring-data/jpa/reference/repositories/projections.html>

定义接口中的 getter 方法，利用接口投影机制

定义 interface

```java
public interface JpaReturnTestVO {

    String getFirstname();
    String getLastname();

    String getName();

    @Value("#{target.firstname + '~' + target.lastname}")
    String getFullName();


    default String getFullName2() {
        return getFirstname().concat("-").concat(getLastname());
    }
}
```

dao 查询

```java
List<JpaReturnTestVO> findByLastname(String lastname);

@Query("select p.firstname as firstname, p.lastname as lastname, p.name as name from Person p where p.name = ?1 order by p.id")
List<JpaReturnTestVO> findAllByName(String name, Pageable pageable);

@Query(nativeQuery = true, value =
        "select p.firstname as firstname, p.lastname as lastname, p.name as name from person p where p.name = ?1 order by p.id")
List<JpaReturnTestVO> findAllByName2(String name);
```

接收返回

```java
List<JpaReturnTestVO> asd = personRepository.findByLastname("asd");
for (JpaReturnTestVO person : asd) {
    System.out.println("person.getName() = " + person.getName());
    System.out.println("person.getFirstname() = " + person.getFirstname());
    System.out.println("person.getLastname() = " + person.getLastname());
}

// 必须要有 alias
List<JpaReturnTestVO> list = personRepository.findAllByName("poi", PageRequest.of(0, 10));
for (JpaReturnTestVO vo : list) {
    System.out.println("vo.getName() = " + vo.getName());
    System.out.println("vo.getFirstname() = " + vo.getFirstname());
    System.out.println("vo.getLastname() = " + vo.getLastname());
    System.out.println("vo.getFullName() = " + vo.getFullName());
    System.out.println("vo.getFullName2() = " + vo.getFullName2());
}

// 使用 nativeQuery
List<JpaReturnTestVO> list2 = personRepository.findAllByName2("poi");
for (JpaReturnTestVO vo : list2) {
    System.out.println("vo2.getName() = " + vo.getName());
    System.out.println("vo2.getFirstname() = " + vo.getFirstname());
    System.out.println("vo2.getLastname() = " + vo.getLastname());
}

```

## 使用 DTO 接收

### 泛型动态查询投影

```java
<T> Optional<T> findById(long id, Class<T> type);

Optional<PersonDTO> dto = personRepository.findById(1, PersonDTO.class);
if (dto.isPresent()) {
    System.out.println("dto = " + dto);
}
```

### 全构造函数

```java
@Query("select new com.example.springquickdemo.dto.PersonDTO(p.name, p.firstname, p.lastname) from Person p where p.firstname = ?1 order by p.id")
List<PersonDTO> findByFirstname(String firstname);
```
