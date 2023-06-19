在表设计规范中，通常建议保留的有两个字段，一个是更新时间，一个是创建时间。常用`@LastModifiedDate` 和 `@CreatedDate`，Hibernate 也提供了类似上述时间注解的功能实现。

```java
@CreationTimestamp  
private Date createTime;  

@UpdateTimestamp  
private Date updateTime;
```


## `@CreationTimestamp` 和 `@CreatedDate` 区别

`@CreatedDate` 是 Spring 注解 [ reference documentation](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#auditing.basics). 它适用于 Spring Data 的所有: JPA, JDBC, R2DBC, MongoDb, Cassandra 等

`@CreationTimestamp` 是 Hibernate 注解 [reference documentation](https://docs.jboss.org/hibernate/orm/5.4/userguide/html_single/Hibernate_User_Guide.html#mapping-generated-CreationTimestamp) 只用于 Hibernate 



## 参考
[Spring Data JPA 的时间注解：@CreatedDate 和 @LastModifiedDate - 简书](https://www.jianshu.com/p/30aef87f3171)