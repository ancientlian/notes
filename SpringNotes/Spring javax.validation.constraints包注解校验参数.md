# Spring javax.validation.constraints包注解校验参数

## 参数校验层级
- 前端校验 
    前端校验一般是为了更快速的响应用户输入，通知用户及时纠正错误输入。
- Controller层校验 
    Controller层直接接受前端传递过来的参数。在这一层建议做参数的“基本校验”。这里的基本校验是指：字段非空判断、数字的最大值和最小值等，这些校验一般是JSR-303注解可以支持的。**这里并不进行业务逻辑的校验，理由是Controller层并不做业务逻辑处理，因此不做业务逻辑校验。**
- Service层校验 
    Service层实现业务逻辑，在这一层需要进行业务逻辑的参数校验。此外，当Service层的接口通过网关暴露时，则需要进行基本校验和业务逻辑参数校验。
- DAO层校验 
    DAO层只能有本地的Service层方法来调用。一般在Service层做参数校验，这一层就不用做校验。

## @Valid 和 @Validated

在Spring中，我们使用`@Valid` 注解进行方法级别验证，同时还能用它来标记成员属性以进行验证。

但是，此注释不支持分组验证。`@Validated`则支持**分组验证**。

@Valid 进行校验的时候，需要用 BindingResult 来做一个校验结果接收。当校验不通过的时候，如果手动不 return ，则并不会阻止程序的执行

@Validated 进行校验的时候，当校验不通过的时候，程序会抛出400异常，阻止方法中的代码执行，这时需要再写一个全局校验异常捕获处理类，然后返回校验提示。

## 引入依赖

引入依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

其中主要依赖为子包`org.hibernate.validator:hibernate-validator`, 非spring直接引入子包

## 可用注解

### 空

| 空检查    |                                                                                |
| --------- | ------------------------------------------------------------------------------ |
| @Null     | 验证对象是否为null                                                             |
| @NotNull  | 验证对象是否不为null, 无法查检长度为0的字符串                                  |
| @NotBlank | 检查约束字符串是不是Null还有被trim的长度是否大于0,只对字符串,且会去掉前后空格. |
| @NotEmpty | 检查约束元素是否为NULL或者是EMPTY.                                             |

### 布尔

| 布尔检查         | 说明                            |
| ------------ | ------------------------------- |
| @AssertTrue  | 检查 Boolean 对象是否为 true。  |
| @AssertFalse | 检查 Boolean 对象是否为 false。 |

### 长度

| 长度                | 说明                                                               |
| ------------------- | ------------------------------------------------------------------ |
| @Size(min=, max=)   | 检查对象`(Array,Collection,Map,String)`长度是否在给定的范围之内 。 |
| @Length(min=, max=) | 检查字符串`(String)`长度是否在给定的范围之内。                     |

### 日期

| 日期     | 说明                                            |
| -------- | ----------------------------------------------- |
| @Past    | 检查`Date`和`Calendar`对象是否在当前时间之前 。 |
| @Future  | 检查`Date`和`Calendar`对象是否在当前时间之后 。 |
| @Pattern | 检查`String`对象是否符合**正则表达式**的规则。  |

### 数值

| 数值                        | 说明                                                                                                                 |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| @Min                        | 检查 Number 和 String 对象是否大等于指定的值 。                                                                      |
| @Max                        | 检查 Number 和 String 对象是否小等于指定的值 。                                                                      |
| @DecimalMax                 | 被标注的值必须不大于约束中指定的最大值。 这个约束的参数是一个通过`BigDecimal`定义的最大值的字符串表示.小数存在精度。 |
| @DecimalMin                 | 被标注的值必须不小于约束中指定的最小值.。这个约束的参数是一个通过`BigDecimal`定义的最小值的字符串表示.小数存在精度。 |
| @Digits                     | 检查 Number 和 String 的构成是否合法 。                                                                              |
| @Digits(integer=,fraction=) | 检查字符串是否是符合指定格式的数字，`interger`指定整数精度，`fraction`指定小数精度。                                 |
| @Range(min=, max=)          | 检查数字是否介于min和max之间。                                                                                       |
| @Negative                   | 负数; 为null有效，0无效                                                                                           |
| @NegativeOrZero             | 负数或零; 为null有效                                                                                                |
| @Positive                   | 正数; 为null有效，0无效                                                                                               |
| @PositiveOrZero             | 正数或零; 为null有效                                                                                            |

建议在**Stirng和Integer类型**使用，不建议在**int类型**上使用。
因为表单值为`""`时无法转换为int，但`String`可以转为`""`，Integer为`null`。

### 其他

| 其他                                           | 说明                                                                                                                                            |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| @Valid                                         | 递归的对关联对象进行校验, 如果关联对象是个集合或者数组,那么对其中的元素进行递归校验,如果是一个map,则对其中的值部分进行校验.(是否进行递归验证)。 |
| @CreditCardNumber                              | 信用卡验证。                                                                                                                                    |
| @Email                                         | 验证是否是邮件地址，**如果为null,不进行验证，算通过验证**。                                                                                     |
| @ScriptAssert(lang=, script=, alias=)          | 检查脚本语言是否符合预期                                                                                                                        |
| @URL(protocol=, host=, port=, regexp=, flags=) | 检查URL是否符合预期                                                                                                                             |

### 示例

```java
class Profile{
        @NotNull(message = "字段值不能为空")
        private String name;
        
        @NotNull
        private String sex;
        
        @Max(value = 20,message = "最大长度为20")
        private String address;
        
        @NotNull
        @Size(max=10,min=5,message = "字段长度要在5-10之间")
        private String fileName;
        
        @Pattern(regexp = "^[a-zA-Z0-9_.-]+@[a-zA-Z0-9-]+(\\.[a-zA-Z0-9-]+)*\\.[a-zA-Z0-9]{2,6}$",message = "不满足邮箱正则表达式")
        private String email;
        
        @AssertTrue(message = "字段为true才能通过")
        private boolean isSave;
        
        @Future(message = "时间在当前时间之后才可以通过")
        private Date date;
 
}
```


## 单个参数校验

对于单个参数的校验，没有用`DTO对象`来接收的参数也可以校验，先在`controller`类上添加`@Validated`，再在对应的参数前加`校验注解`

```java
@RestController
@RequestMapping("/user")
@Validated
public class UserController {
    @PostMapping("/registry")
    public ResponseResult registryUser(@NotBlank(message = "name不能为空") String name) {
        return ResponseResult.okResult(name);
    }
}
```

## 自定义校验注解

### 1.创建自定义注解

Status.java
```java
import javax.validation.Constraint;  
import javax.validation.Payload;  
import java.lang.annotation.*;  
  
/**  
 * @author lian  
 */
@Target({ElementType.FIELD, ElementType.PARAMETER})  
@Retention(RetentionPolicy.RUNTIME)  
@Documented  
@Constraint(validatedBy = {StatusValidator.class})  
public @interface Status {  
    String[] statusType() default {};  
  
    String message() default "状态传递有误";  
  
    Class<?>[] groups() default {};  
  
    Class<? extends Payload>[] payload() default {};  
  
  
}
```

`require`中存放是否必要的布尔值，`message`中存放错误提示信息(默认提示的消息，可以指定资源配置文件中的key，也可以直接写死,如`String message() default "{com.example.springboot.validate.message}"; ` )

`@Constraint`注解中对具体的参数验证类进行指定

### 2.创建注解执行器

StatusValidator.java
```java
 
import javax.validation.ConstraintValidator;  
import javax.validation.ConstraintValidatorContext;  
import java.util.Arrays;  
import java.util.List;  
  
/**  
 * @author lian  
 */
 public class StatusValidator implements ConstraintValidator<Status, Integer> {  
  
    private List<String> typeStatus;  
  
    @Override  
    public void initialize(Status constraintAnnotation) {  
        typeStatus = Arrays.asList(constraintAnnotation.statusType());  
        ConstraintValidator.super.initialize(constraintAnnotation);  
    }
  
    @Override  
    public boolean isValid(Integer value, ConstraintValidatorContext constraintValidatorContext) {  
        if (value != null) {  
            return typeStatus.contains(String.valueOf(value));  
        }  
        return true;  
    }  
}
```

通过`initialize()`方法获取触发验证的注解，进行属性的设置等前置操作

调用`isValid()`方法进行参数验证，返回false的话，会抛出`BindException`异常; 因为在`isValid`方法中,接收的是`Integer`参数,如果在使用注解时放在了`Double`或`String`上,会报错`javax.validation.UnexpectedTypeException`

### 3. 使用注解

 ```java

import com.example.springquickdemo.annotation.validator.Status;  
import com.example.springquickdemo.response.ServerResponseEntity;  
import org.springframework.validation.annotation.Validated;  
import org.springframework.web.bind.annotation.GetMapping;  
import org.springframework.web.bind.annotation.RequestMapping;  
import org.springframework.web.bind.annotation.RestController;  
  
/**  
 * @author lian  
 */
@RestController  
@RequestMapping("/valid/test")  
@Validated  
public class ValidTestController {  
  
    @GetMapping("/t1")  
    public ServerResponseEntity<String> testFun1(@Status(statusType = {"1", "2"}, message = "param1校验Integer错误") Integer param1,  
	                                             @Status(statusType = {"1", "2"}, message = "param2校验int错误") int param2) {  
  
        System.out.println("================================================");  
        System.out.println(param1);  
        System.out.println(param2);  
        System.out.println("================================================");  
  
        return ServerResponseEntity.success();  
  
    }  
  
}
 ```

### 4.自定义注解返回报错

```java
 
@Controller  
@RestControllerAdvice  
public class DefaultExceptionHandlerConfig {

	@ExceptionHandler(ConstraintViolationException.class)  
	public ResponseEntity<ServerResponseEntity<Map<Path, String>>> handleValidException(ConstraintViolationException e){  
	    // 获取异常信息  
	    Set<ConstraintViolation<?>> constraintViolations = e.getConstraintViolations();  
	    // 将异常信息收集到Map，key为校验失败的字段，value为失败原因  
	    Map<Path, String> errorMap = constraintViolations.stream()  
	            .collect(Collectors.toMap(ConstraintViolation::getPropertyPath, ConstraintViolation::getMessage));  
	    // 返回校验失败信息  
	    return ResponseEntity.status(HttpStatus.OK)  
	            .body(ServerResponseEntity.fail(ResponseEnum.METHOD_ARGUMENT_NOT_VALID,errorMap));  
	    
	    /*  
		List<String> msgList = new ArrayList<>();  
		for (ConstraintViolation<?> constraintViolation : constraintViolations) {  
		    msgList.add(constraintViolation.getPropertyPath() + ":" +constraintViolation.getMessage());
		}  
		return ResponseEntity.status(HttpStatus.OK)  
		        .body(ServerResponseEntity.fail(ResponseEnum.METHOD_ARGUMENT_NOT_VALID, msgList));
        */
	}
	
}
```

## Bean的级联嵌套校验

如果有A，其中有个属性不是基本类型，而是B类型. 此时对A的其他校验会生效，但是对B属性的校验不会生效。

想要A的B属性的校验也生效，主需要在属性上添加@Valid注解即可

```java
public class A {

	@NotBlank
	private String str;
	
	@Valid
	private B b;
}
```

如果想校验带泛型的List，同样在对应位置加`@Valid`注解; 

```java
public class A {

	@NotBlank
	private String str;
	
	private List<@Valid B> b;
}
```

List中每个都会被校验, 并在校验错误结果中标注是List中的哪个下标的元素的哪个字段校验失败

## Service层校验

用于**RPC调用**等场景

接口层
```java
public interface Service {
    void add(@Valid A a);
}
```

实现类
```java
@Service
@Validated
public class ServiceImpl implements Service {
    @Override
    public void add(A a) {}
}

```

==`@Valid`注解一定要标在接口方法需要校验的参数上，不能标在实现类的方法参数上==

对于`@Validated`注解，无论是基本数据类型校验还是Bean校验，都要加这个注解

校验失败的异常是`javax.validation.ConstraintViolationException`

## 分组检验

场景: 如新增id必需为空(数据库自增id), 修改id必需不为空

### 1.在实体类中新建接口作为分组,字段添加分组注解

```java
public class Employee {  
  
    public interface Add{}  
  
    public interface Update{}  
  
    @Null(message = "新增ID必需为空", groups = {Add.class})  
    @NotNull(message = "更新ID必需不为空", groups = {Update.class})  
    private Long id;
    
}
```

### 2. Controller增加参数注解,指定启用分组

```java
@PostMapping("/ee/add")  
public ServerResponseEntity<String> addEmployee(@RequestBody @Validated({Employee.Add.class}) Employee employee){  
    return ServerResponseEntity.success();  
}  
@PutMapping("/ee/update")  
public ServerResponseEntity<String> updateEmployee(@RequestBody @Validated({Employee.Update.class}) Employee employee){  
    return ServerResponseEntity.success();  
}
```

==如果在Controller中的方法给参数指定了校验分组，那么实体类中标注了校验注解，但是**没有指定分组的校验规则将不生效**==

没有指定分组的校验注解，都属于默认`Default`分组,想要增加默认的校验,添加`Default`分组即可

```java
@PostMapping("/ee/add")  
public ServerResponseEntity<String> addEmployee(@RequestBody @Validated({Employee.Add.class, Default.class}) Employee employee){  
    return ServerResponseEntity.success();  
}  
@PutMapping("/ee/update")  
public ServerResponseEntity<String> updateEmployee(@RequestBody @Validated({Employee.Update.class, Default.class}) Employee employee){  
    return ServerResponseEntity.success();  
}
```

### 集合的分组校验

接收参数为`List`集合,分组校验会失效,需要**自定义校验注解**。创建注解`@ValidListGroup`，自定义一个属性表示用哪个分组对集合进行校验

自定义注解
```java
package com.example.springquickdemo.annotation.validator;  
  
import javax.validation.Constraint;  
import javax.validation.Payload;  
import javax.validation.groups.Default;  
import java.lang.annotation.*;  
  
/**  
 * @author lian  
 */
@Target({ElementType.FIELD, ElementType.PARAMETER})  
@Retention(RetentionPolicy.RUNTIME)  
@Documented  
@Constraint(validatedBy = {ListGroupValidator.class})  
public @interface ValidListGroup {  
  
    /**  
     * 自定义分组校验属性  
     */  
    Class<?>[] groupings() default {Default.class};  
  
    String message() default "分组校验参数有误";  
  
    Class<?>[] groups() default {};  
  
    Class<? extends Payload>[] payload() default {};  
  
    /**  
     * 快速失败  
     */  
    boolean quickFail() default false;  
  
}
```

自定义校验器
```java
package com.example.springquickdemo.annotation.validator;  
  
import com.example.springquickdemo.exception.ListGroupValidException;  
import com.example.springquickdemo.util.ValidatorUtils;  
  
import javax.validation.ConstraintValidator;  
import javax.validation.ConstraintValidatorContext;  
import javax.validation.ConstraintViolation;  
import java.util.HashMap;  
import java.util.List;  
import java.util.Map;  
import java.util.Set;  
  
/**  
 * @author lian  
 */public class ListGroupValidator implements ConstraintValidator<ValidListGroup, List> {  
  
    /**  
     * 自定义注解@ValidListGroup中指定的校验分组  
     */  
    private Class<?>[] groupings;  
  
    private boolean quickFail;  
  
    /**  
     * Initializes the validator in preparation for     */    @Override  
    public void initialize(ValidListGroup constraintAnnotation) {  
        groupings = constraintAnnotation.groupings();  
        quickFail = constraintAnnotation.quickFail();  
        ConstraintValidator.super.initialize(constraintAnnotation);  
    }  
  
    /**  
     * Implements the validation logic.     */    @Override  
    public boolean isValid(List value, ConstraintValidatorContext context) {  
        Map<Integer, Set<ConstraintViolation<Object>>> errors = new HashMap<>(16);  
        for (int i = 0; i < value.size(); i++) {  
            Object object = value.get(i);  
            // 用工具类获取validator对象，进行分组校验，返回错误结果  
            Set<ConstraintViolation<Object>> error = ValidatorUtils.validator.validate(object, groupings);  
            if (error.size() > 0) {  
                errors.put(i, error);  
                if (quickFail) {  
                    throw new ListGroupValidException(errors);  
                }  
            }  
        }  
        if (errors.size() > 0) {  
            throw new ListGroupValidException(errors);  
        }  
        return true;  
    }  
}


@Component  
public class ValidatorUtils {  
    public static Validator validator;  
  
    /**  
     * 注入Validator对象  
     */  
    @Autowired  
    public void setValidator(Validator validator) {  
        ValidatorUtils.validator = validator;  
    }  
}
```

自定义异常
```java
@Getter  
@Setter  
public class ListGroupValidException extends RuntimeException {  
  
    private Map<Integer, Set<ConstraintViolation<Object>>> errors;  
  
    public ListGroupValidException(Map<Integer, Set<ConstraintViolation<Object>>> errors) {  
        this.errors = errors;  
    }  
  
}
```

异常处理器处理自定义异常
```java
/**  
 * 处理自定义集合分组校验注解的校验失败异常  
 */  
@ExceptionHandler(ValidationException.class)  
public ResponseEntity<ServerResponseEntity<?>> handleValidException(ValidationException e) {  
    log.error("ValidationException ", e);  
  
    Map<Integer, Map<Path, String>> errorMap = new HashMap<>(8);  
    Throwable throwable = e.getCause();  
    ListGroupValidException exception = (ListGroupValidException) throwable;  
    Map<Integer, Set<ConstraintViolation<Object>>> errors = exception.getErrors();  
    // 将异常信息收集到Map，key为集合中校验失败元素的索引，value为校验失败字段和原因  
    errors.forEach((k, v) -> {  
        errorMap.put(k, v.stream().collect(Collectors.toMap(ConstraintViolation::getPropertyPath, ConstraintViolation::getMessage)));  
    });  
    return ResponseEntity.status(HttpStatus.OK)  
            .body(ServerResponseEntity.fail(ResponseEnum.METHOD_ARGUMENT_NOT_VALID, errorMap));  
}
```

在Controller对应的方法中使用自定义注解,已经可以实现集合的分组校验. 如果想要一旦对某个对象的某个字段校验失败，立即停止校验，返回错误，开启快速失败模式即可

```java
@PostMapping("/ee/addList")  
public ServerResponseEntity<String> addEmployeeList(@RequestBody @ValidListGroup(groupings = {Employee.Add.class, Default.class}) List<Employee> employeeList) {  
    return ServerResponseEntity.success();  
}  
  
@PutMapping("/ee/updateList")  
public ServerResponseEntity<String> updateEmployeeList(@RequestBody @ValidListGroup(groupings = {Employee.Update.class, Default.class}) List<Employee> employeeList) {  
    return ServerResponseEntity.success();  
}
```

开启快速失败`quickFail = true`
```java
@PutMapping("/ee/updateList")  
public ServerResponseEntity<String> updateEmployeeList(@RequestBody @ValidListGroup(groupings = {Employee.Update.class, Default.class}, quickFail = true) List<Employee> employeeList) {  
    return ServerResponseEntity.success();  
}
```

## Bean属性之间的关联校验

场景: 添加、更新员工2合1的方法, 传入的对象id为null，就是添加，id不为null就是更新. 添加时员工姓名不能为空，修改时可以为空; 员工姓名需要根据id的值是否为null来决定是否采用非空校验

实现
### 1.添加字段校验注解,指定分组

```java
@NotBlank(message = "员工姓名不能为空", groups = Add.class)  
private String name;
```

### 2.校验分组处理器

```java
public class EmployeeGroupSequenceProvider implements DefaultGroupSequenceProvider<Employee> {  
  
    @Override  
    public List<Class<?>> getValidationGroups(Employee employee) {  
        // 创建需要校验的分组集合  
        List<Class<?>> defaultGroupSequence = new ArrayList<>();  
        // 添加默认分组Default  
        defaultGroupSequence.add(Employee.class);  
        // 根据employee的id是否为空，决定是否加上Add组  
        Optional.ofNullable(employee).ifPresent(e -> {  
            Long id = e.getId();  
            if (id == null) {  
                // id为空，加上Add组  
                defaultGroupSequence.add(Employee.Add.class);  
            }  
        });  
        return defaultGroupSequence;  
    }  
}
```

### 3.添加校验注解

```java
@GroupSequenceProvider(EmployeeGroupSequenceProvider.class)  
public class Employee {

	@NotBlank(message = "员工姓名不能为空", groups = Add.class)  
	private String name;
	
	......
}
```

## 统一异常处理

### 使用`BindingResult`

在Controller的请求处理方法参数声明中，在需要校验的Bean的形参前面加上`@Valid`注解，并在该Bean的形参后面紧跟着声明一个`BindingResult`类型的参数(注意，**中间不能夹杂任何其他类型的参数**)，这样校验的结果就会保存在`BindingResult`对象中

```java
@RestController
@RequestMapping("validate")
public class ValidateController {
    @PostMapping("test1")
    public String testValidate(@RequestBody @Valid User user, BindingResult bindingResult){
        System.out.println(user);
        System.out.println(bindingResult);
        return "success";
    }
}
```

如果我们在Controller方法参数中不加`BindingResult`对象捕获校验失败信息，那么校验失败信息会以`org.springframework.web.bind.MethodArgumentNotValidException`异常形式被异常解析器处理

如果Controller中需要校验的参数是带泛型的List，那么在参数对应位置标注`@Valid`，同时要在类上标注`@Validated`，这样校验才会生效。并且效果是针对List中的每个对象都做校验; 校验失败后会抛出`javax.validation.ConstraintViolationException`异常，并且会用从0开始的索引来表明是List中的哪个对象的字段校验不通过

### 

## 快速失败(提前校验返回)

如果我们想在某个字段校验不通过后立即返回，不再继续校验其他字段，可以配置**快速失败模式**。

配置方式为创建一个校验配置类，往容器中注入一个快速失败校验器，替换掉默认的校验器

```java
import org.hibernate.validator.HibernateValidator;  
import org.springframework.context.annotation.Bean;  
import org.springframework.context.annotation.Configuration;  
  
import javax.validation.Validation;  
import javax.validation.Validator;  
import javax.validation.ValidatorFactory;  
  
  
@Configuration  
public class ValidationConfig {  
    /**  
     * 配置校验框架 快速返回模式  
     */  
    @Bean  
    public Validator validator() {  
        ValidatorFactory validatorFactory = Validation.byProvider(HibernateValidator.class)  
                .configure()  
                .failFast(false)  
                .buildValidatorFactory();  
        return validatorFactory.getValidator();  
    }  
}
```


## 参考

[SpringBoot后端数据校验实战](https://baobao555.tech/archives/47)
[SpringMVC参数校验（针对\`@RequestBody\`返回\`400\`） - Ryan.Miao - 博客园](https://www.cnblogs.com/woshimrf/p/spring-web-400.html)
