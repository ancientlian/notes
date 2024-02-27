## 5.1.AOP概念

面向切片编程(AOP)是对面向对象编程(OOP)的补充，它提供了考虑程序结构的另一种方式。我们需要关注的部分就是横切关注点。比如说日志、安全、缓存、事务等……

- **切面 Aspect:** A modularization of a concern that cuts across multiple classes. Transaction management is a good example of a crosscutting concern in enterprise Java applications. In Spring AOP, aspects are implemented by using regular classes (the [schema-based approach](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-schema)) or regular classes annotated with the `@Aspect` annotation (the [@AspectJ style](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-ataspectj)).
- **连接点 Join point:** A point during the execution of a program, such as the execution of a method or the handling of an exception. In Spring AOP, a join point always represents a method execution.
- **通知 Advice**: Action taken by an aspect at a particular join point. Different types of advice include “around”, “before” and “after” advice. (Advice types are discussed later.) Many AOP frameworks, including Spring, model an advice as an interceptor and maintain a chain of interceptors around the join point.
- **切入点 Pointcut:** A predicate that matches join points. Advice is associated with a pointcut expression and runs at any join point matched by the pointcut (for example, the execution of a method with a certain name). The concept of join points as matched by pointcut expressions is central to AOP, and Spring uses the AspectJ pointcut expression language by default.
- **引入 Introduction:** Declaring additional methods or fields on behalf of a type. Spring AOP lets you introduce new interfaces (and a corresponding implementation) to any advised object. For example, you could use an introduction to make a bean implement an `IsModified` interface, to simplify caching. (An introduction is known as an inter-type declaration in the AspectJ community.)
- **目标对象 Target object:** An object being advised by one or more aspects. Also referred to as the “advised object”. Since Spring AOP is implemented by using runtime proxies, this object is always a proxied object.
- **AOP 代理 AOP proxy:** An object created by the AOP framework in order to implement the aspect contracts (advise method executions and so on). In the Spring Framework, an AOP proxy is a JDK dynamic proxy or a CGLIB proxy.
- **织入 Weaving:** linking aspects with other application types or objects to create an advised object. This can be done at compile time (using the AspectJ compiler, for example), load time, or at runtime. Spring AOP, like other pure Java AOP frameworks, performs weaving at runtime.

### advice类型

- 前置通知 Before advice: Advice that runs before a join point but that does not have the ability to prevent execution flow proceeding to the join point (unless it throws an exception).在连接点之前运行的通知，但不能阻止执行流继续到连接点(除非它抛出异常)。
- 后置返回通知 After returning advice: Advice to be run after a join point completes normally (for example, if a method returns without throwing an exception).在连接点正常完成后运行的通知(例如，如果一个方法返回而没有抛出异常)。
- 后置异常通知 After throwing advice: Advice to be run if a method exits by throwing an exception.在方法退出时通过抛出异常来运行的通知。
- 后置（最终）通知 After (finally) advice: Advice to be run regardless of the means by which a join point exits (normal or exceptional return).无论连接点以何种方式退出(正常或异常返回)，都要运行的通知。
- 环绕通知 Around advice: Advice that surrounds a join point such as a method invocation. This is the most powerful kind of advice. Around advice can perform custom behavior before and after the method invocation. It is also responsible for choosing whether to proceed to the join point or to shortcut the advised method execution by returning its own return value or throwing an exception.围绕连接点(如方法调用)的通知。Around通知可以在方法调用之前和之后执行自定义行为。它还负责选择是继续到连接点，还是通过返回自己的返回值或抛出异常来缩短被建议的方法执行。

## 使用spring实现AOP

导入一个依赖

```xml
 <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.6</version>
    <scope>runtime</scope>
</dependency>
```

关于aop相关的主要引入了支持切面编程的依赖：`org.aspectj.aspectjweaver`和`org.aspectj.aspectjrt`的依赖。`aspectjweaver`是aspectj的织入包，`aspectjrt`是aspectj的运行时包。

方式一：使用spring的API接口

方式二：自定义来实现aop

方法三：注解实现

## 5.4.3.申明切入点
