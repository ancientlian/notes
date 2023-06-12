下载方式：

官网下载

使用idea创建


# 自动装配原理

## pom.xml的父依赖

## 启动器

默认的主启动类

```java
//@SpringBootApplication 来标注一个主程序类
//说明这是一个Spring Boot应用
@SpringBootApplication
public class SpringbootApplication {
   public static void main(String[] args) {     
   //以为是启动了一个方法，没想到启动了一个服务      			
   SpringApplication.run(SpringbootApplication.class, args);   }
}
```

## SpringApplication.run分析

**SpringApplication这个类主要做了以下四件事情：**

1、推断应用的类型是普通的项目还是Web项目

2、查找并加载所有可用初始化器 ， 设置到initializers属性中

3、找出所有的应用程序监听器，设置到listeners属性中

4、推断并设置main方法的定义类，找到运行的主类

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7L1vFQMnaRIJSmeZ58T2eZicjafiawQLp9u8wc4ic1Mjy6OyfibzfjVofeL5pnS1NSFKVjlIg6neI9ySg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



# 配置

## yaml注入配置文件

```java
/*
    @ConfigurationProperties作用：
    将配置文件中配置的每一个属性的值，映射到这个组件中；
    告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定
    参数 prefix = “person” : 将配置文件中的person下面的所有属性一一对应
*/
@Data
@AllArgsConstructor
@NoArgsConstructor
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
}
```

## 加载指定的配置文件

**@PropertySource ：**加载指定的配置文件；

```java
@PropertySource(value = "classpath:person.properties")
@Component //注册bean
public class Person {
    @Value("${name}")    
    private String name;
    ......  
}
```

## 配置文件占位符

```yaml
person:
  name: Alice${random.uuid} # 随机uuid
  age: ${random.int}  # 随机int
  happy: false
  birth: 2000/01/01
  maps: {k1: v1,k2: v2}
  lists:
    - code
    - girl
    - music
  dog:
    name: ${person.hello:other}_旺财
    age: 1
```

## 小结

![图片](https://mmbiz.qpic.cn/mmbiz_png/uJDAUKrGC7KtjyIb9NEaYlz0tCWSiboOYjMibiaov73iaTsiaWEPoArDcAB1Ooibx9uR5JxtacIuicHblEtUI9SrySX2A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)1、@ConfigurationProperties只需要写一次即可 ， @Value则需要每个字段都添加

2、松散绑定：这个什么意思呢? 比如我的yml中写的last-name，这个和lastName是一样的，`-`后面跟着的字母默认是大写的。这就是松散绑定。可以测试一下

3、JSR303数据校验 ， 这个就是我们可以在字段是增加一层过滤器验证 ， 可以保证数据的合法性

4、复杂类型封装，yml中可以封装对象 ， 使用value就不支持

## JSR303数据校验

![Spring javax.validation.constraints包注解校验参数](../SpringNotes/Spring%20javax.validation.constraints包注解校验参数.md)

# 配置项 External Application Properties

> 7.2.3. External Application Properties
> Spring Boot will automatically find and load `application.properties` and `application.yaml` files from the following locations when your application starts:
> 1. From the classpath
> 	1. The classpath root
> 	2. The classpath `/config` package
> 2. From the current directory
>	1. The current directory
>	2. The `config/` subdirectory in the current directory
>	3. Immediate child directories of the `config/` subdirectory

[Spring Boot Reference Documentation](https://docs.spring.io/spring-boot/docs/2.7.12/reference/htmlsingle/#features.external-config.files)

`config/yaml > yaml > resourse/config/yaml > resourse/yaml`

`properties > yaml`

# 多环境配置

application.yaml

```yaml
server:
  port: 8081  # 必须要有空格
spring:
  profiles:
    active: test

---
server:
  port: 8082
spring:
  config:
    activate:
      on-profile: dev
---

server:
  port: 8083
spring:
  config:
    activate:
      on-profile: test
```

2.4.x新版本特性修改，现在properties也可以多环境配置了



# 自动配置原理

- 一但这个配置类生效；这个配置类就会给容器中添加各种组件；
- 这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的；
- 所有在配置文件中能配置的属性都是在xxxxProperties类中封装着；
- 配置文件能配置什么就可以参照某个功能对应的这个属性类

## 精髓

1、SpringBoot启动会加载大量的自动配置类

2、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；

3、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）

4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；

**xxxxAutoConfigurartion：自动配置类；**给容器中添加组件

**xxxxProperties:封装配置文件中相关属性；**

## 了解：@Conditional





**我们可以通过启用 debug=true属性；来让控制台打印自动配置报告，这样我们就可以很方便的知道哪些自动配置类生效；**

**Positive matches:（自动配置类启用的：正匹配）**

**Negative matches:（没有启动，没有匹配成功的自动配置类：负匹配）**

**Unconditional classes: （没有条件的类）**