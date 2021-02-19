在Spring中有三种装配的方式
1.在xml中显示的配置
2.在java中显示配置
3.隐式的自动装配bean [重要]



## byName

```xml
    <bean id="cat" class="com.lian.pojo.Cat"/>
    <bean id="dog" class="com.lian.pojo.Dog"/>

    <!--
    byName:会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid!
    -->
    <bean id="people" class="com.lian.pojo.People" autowire="byName">
        <property name="name" value="lian"/>
        <!--
        <property name="cat" ref="cat"/>
        <property name="dog" ref="dog"/>
        -->
   </bean>
```



## byType

```xml
    <bean class="com.lian.pojo.Cat"/>
    <bean class="com.lian.pojo.Dog"/>

    <!--
    byName:会自动在容器上下文中查找，和自己对象set方法后面的值对应的beanid!
    byType:会自动在容器上下文中查找，和自己对象属性类型相同的bean !
    -->
    <bean id="people" class="com.lian.pojo.People" autowire="byType">
        <property name="name" value="lian"/>
        <!--
        <property name="cat" ref="cat"/>
        <property name="dog" ref="dog"/>
        -->
   </bean>
```

可以直接省略id名字



## 使用注解实现自动装配

使用注解：

1.导入约束context

2.添加配置`<context:annotation-config/>`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```



## @Autowired自动装配

您可以将@Autowired注释应用于**构造函数**

```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

如果有多个构造函数可用，而且没有主构造函数/默认构造函数，那么至少必须用@Autowired对其中一个构造函数进行注释，以便指示容器使用哪个构造函数



还可以将@Autowired注释应用于**传统的setter方法**

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```



还可以将注释应用于**具有任意名称和多个参数的方法**

```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```



你也可以把@Autowired应用到**字段**上，甚至把它**和构造函数混合**

```java
public class MovieRecommender {

    private final CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    private MovieCatalog movieCatalog;

    @Autowired
    public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```



### @Nullable作用

Autowired有一个参数可以设置，如果显示定义了Autowired的required属性为false，说明这个对象可以为null（使框架跳过不满足的注入点），否则不允许为空。默认情况是注入的时候如果没有匹配的bean，自用装配会失效。

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Autowired(required = false)
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // ...
}
```

```java
public @interface Autowired {
    boolean required() default true;
}
```



同样的，另外，我们可以通过Java 8的`java.util.Optional`表示特定依赖项的非必需性质，如以下示例所示：

```java
public class SimpleMovieLister {

    @Autowired
    public void setMovieFinder(Optional<MovieFinder> movieFinder) {
        ...
    }
}
```

从Spring Framework 5.0开始，就可以使用**@Nullable**注解

```java
public class SimpleMovieLister {

    @Autowired
    public void setMovieFinder(@Nullable MovieFinder movieFinder) {
        ...
    }
}
```



## 使用@Qualifier微调配合

您可以将限定符值与特定的参数关联起来，缩小类型匹配的范围，以便为每个参数选择特定的bean。

如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired]】完成的时候，我们可以
使用`@Qualifier(value="xx")`去配置@Autowired的使用，指定一个唯一的bean对象注入!

如下面的例子所示:

```java
public class MovieRecommender {

    @Autowired
    @Qualifier("main")
    private MovieCatalog movieCatalog;

    // ...
}
```

还可以**在各个构造函数参数或方法参数上指定**`@Qualifier`批注，如以下示例所示：

```java
public class MovieRecommender {

    private MovieCatalog movieCatalog;

    private CustomerPreferenceDao customerPreferenceDao;

    @Autowired
    public void prepare(@Qualifier("main") MovieCatalog movieCatalog,
            CustomerPreferenceDao customerPreferenceDao) {
        this.movieCatalog = movieCatalog;
        this.customerPreferenceDao = customerPreferenceDao;
    }

    // ...
}
```

以上使用的bean的定义

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="main"/> 

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="action"/> 

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```



## @Resource注入

`@Resource`带有一个name属性。默认情况下，Spring将该值解释为要注入的bean名。也就是它遵循**按名查找**（by-name semantics），如下面的例子所示:

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Resource(name="myMovieFinder") 
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

如果没有显式指定名称，则默认名称派生字段名或setter方法。对于字段，它采用字段名。对于setter方法，它采用bean属性名。下面的例子将把名为movieFinder的bean注入到其setter方法中:

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Resource
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

**注：在没有指定显式名称使用@Resource的特殊情况下，与@Autowired相似，@Resource查找主类型匹配（byType）而不是特定的命名bean（byName）并解析，并解析依赖项:BeanFactory、ApplicationContext、ResourceLoader、ApplicationEventPublisher和MessageSource接口。**

在下面的例子中，`customerPreferenceDao`字段首先查找名为“customerPreferenceDao”的bean，然后返回到`customerPreferenceDao`类型的主类型匹配:

```java
public class SimpleMovieLister {

    private MovieFinder movieFinder;

    @Resource
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```



## @Autowired与@Resource两者异同

两者都是自动装配，都可以放在属性字段上。

- @ Autowired通过byType的方式实现，而且必须要求这个对象存在! 【常用】
- @ Resource默认通过byName的方式实现，如果找不到名字,则通过byType实现！
  如果两个都找不到的情况下，就报错! 【常用】
- 执行顺序不同: @ Autowired 通过byType的方式实现。 ，