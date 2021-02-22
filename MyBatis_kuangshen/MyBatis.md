## 持久化

数据持久化

- 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
- 内存:断电即失

- 数据库(Jdbc), io文件持久化。

- 生活:冷藏.罐头。

为什么需要持久化?

- 有一些对象，不能让他丢掉。
- 内存太贵了

## 持久层

Dao层，Service层， Controller层...

- 完成持久化工作的代码块
- 层界限十分明显。



## 为什么需要MyBatis

帮助程序猿将数据存入到数据库中。
方便
传统的JDBC代码太复杂了。简化。框架。自动化。
不用Mybatis也可以。更容易上手。技术没有高低之分
优点:

- 简单易学
- 灵活
- sql和代码的分离，提高了可维护性。
- 提供映射标签,支持对象与数据库的orm字段关系映射
- 提供对象关系映射标签，支持对象关系组建维护
- 提供xml标签, 支持编写动态sql。



## 第一个MyBatis

### 搭建数据库

### 创建一个模块

#### 编写配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost/school?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=utf8&amp;useSSL=true"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>




</configuration>
```

编写工具类



### 编写代码

- 实体类
- Dao接口

```java
public interface UserDao {
    List<User> getUserList();
}
```

- 接口实现类由原来的userDaolmpl转变为一个mapper对象





### 测试

注意点报错原因

```
org.apache.ibatis.binding.BindingException: Type interface com.lian.dao.UserDao is not known to the MapperRegistry.
```

解决方法：

maven由于他的约定大于配置，我们之后遇见我们写的配置文件，无法被导出或者生效的问题，解决方案：

```xml
<resources>
      <resource>
        <directory>src/main/java</directory>
        <includes>
          <include>**/*.xml</include>
        </includes>
      </resource>
      <resource>
        <directory>src/main/resources</directory>
        <includes>
          <include>*.xml</include>
        </includes>
      </resource>
</resources>
```



遇见报错

```java
D:\Java\jdk-11.0.9\bin\java.exe -ea -Didea.test.cyclic.buffer.size=1048576 "-javaagent:D:\JetBrains\IntelliJ IDEA 2020.3.1\lib\idea_rt.jar=6063:D:\JetBrains\IntelliJ IDEA 2020.3.1\bin" -Dfile.encoding=UTF-8 -classpath "D:\JetBrains\IntelliJ IDEA 2020.3.1\lib\idea_rt.jar;D:\JetBrains\IntelliJ IDEA 2020.3.1\plugins\junit\lib\junit5-rt.jar;D:\JetBrains\IntelliJ IDEA 2020.3.1\plugins\junit\lib\junit-rt.jar;D:\IdeaProjects\MyBatisStudy\mybatis01\target\test-classes;D:\IdeaProjects\MyBatisStudy\mybatis01\target\classes;D:\Java\maven_repository\org\mybatis\mybatis\3.5.6\mybatis-3.5.6.jar;D:\Java\maven_repository\junit\junit\4.13.1\junit-4.13.1.jar;D:\Java\maven_repository\org\hamcrest\hamcrest-core\1.3\hamcrest-core-1.3.jar;D:\Java\maven_repository\mysql\mysql-connector-java\8.0.23\mysql-connector-java-8.0.23.jar;D:\Java\maven_repository\com\google\protobuf\protobuf-java\3.11.4\protobuf-java-3.11.4.jar;D:\Java\maven_repository\commons-dbcp\commons-dbcp\1.4\commons-dbcp-1.4.jar;D:\Java\maven_repository\commons-pool\commons-pool\1.5.4\commons-pool-1.5.4.jar" com.intellij.rt.junit.JUnitStarter -ideVersion5 com.lian.dao.UserDaoTest,test

java.lang.ExceptionInInitializerError
	at com.lian.dao.UserDaoTest.test(UserDaoTest.java:15)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:59)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:56)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.BlockJUnit4ClassRunner$1.evaluate(BlockJUnit4ClassRunner.java:100)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:366)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:103)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:63)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:69)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:33)
	at com.intellij.rt.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:220)
	at com.intellij.rt.junit.JUnitStarter.main(JUnitStarter.java:53)
Caused by: org.apache.ibatis.exceptions.PersistenceException: 
### Error building SqlSession.
### Cause: org.apache.ibatis.builder.BuilderException: Error creating document instance.  Cause: org.xml.sax.SAXParseException; lineNumber: 6; columnNumber: 5; 1 字节的 UTF-8 序列的字节 1 无效。
	at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:80)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:64)
	at com.lian.utils.MyBatisUtils.<clinit>(MyBatisUtils.java:19)
	... 26 more
Caused by: org.apache.ibatis.builder.BuilderException: Error creating document instance.  Cause: org.xml.sax.SAXParseException; lineNumber: 6; columnNumber: 5; 1 字节的 UTF-8 序列的字节 1 无效。
	at org.apache.ibatis.parsing.XPathParser.createDocument(XPathParser.java:263)
	at org.apache.ibatis.parsing.XPathParser.<init>(XPathParser.java:127)
	at org.apache.ibatis.builder.xml.XMLConfigBuilder.<init>(XMLConfigBuilder.java:82)
	at org.apache.ibatis.session.SqlSessionFactoryBuilder.build(SqlSessionFactoryBuilder.java:77)
	... 28 more
Caused by: org.xml.sax.SAXParseException; lineNumber: 6; columnNumber: 5; 1 字节的 UTF-8 序列的字节 1 无效。
	at java.xml/com.sun.org.apache.xerces.internal.util.ErrorHandlerWrapper.createSAXParseException(ErrorHandlerWrapper.java:204)
	at java.xml/com.sun.org.apache.xerces.internal.util.ErrorHandlerWrapper.fatalError(ErrorHandlerWrapper.java:178)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLErrorReporter.reportError(XMLErrorReporter.java:400)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLErrorReporter.reportError(XMLErrorReporter.java:306)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLDocumentScannerImpl$PrologDriver.next(XMLDocumentScannerImpl.java:1000)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLDocumentScannerImpl.next(XMLDocumentScannerImpl.java:605)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLDocumentFragmentScannerImpl.scanDocument(XMLDocumentFragmentScannerImpl.java:534)
	at java.xml/com.sun.org.apache.xerces.internal.parsers.XML11Configuration.parse(XML11Configuration.java:888)
	at java.xml/com.sun.org.apache.xerces.internal.parsers.XML11Configuration.parse(XML11Configuration.java:824)
	at java.xml/com.sun.org.apache.xerces.internal.parsers.XMLParser.parse(XMLParser.java:141)
	at java.xml/com.sun.org.apache.xerces.internal.parsers.DOMParser.parse(DOMParser.java:246)
	at java.xml/com.sun.org.apache.xerces.internal.jaxp.DocumentBuilderImpl.parse(DocumentBuilderImpl.java:339)
	at org.apache.ibatis.parsing.XPathParser.createDocument(XPathParser.java:261)
	... 31 more
Caused by: com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: 1 字节的 UTF-8 序列的字节 1 无效。
	at java.xml/com.sun.org.apache.xerces.internal.impl.io.UTF8Reader.invalidByte(UTF8Reader.java:702)
	at java.xml/com.sun.org.apache.xerces.internal.impl.io.UTF8Reader.read(UTF8Reader.java:568)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLEntityScanner.load(XMLEntityScanner.java:1904)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLEntityScanner.scanData(XMLEntityScanner.java:1377)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLScanner.scanComment(XMLScanner.java:800)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLDocumentFragmentScannerImpl.scanComment(XMLDocumentFragmentScannerImpl.java:1069)
	at java.xml/com.sun.org.apache.xerces.internal.impl.XMLDocumentScannerImpl$PrologDriver.next(XMLDocumentScannerImpl.java:883)
	... 39 more


Process finished with exit code -1

```

出现这个问题的原因很简单，是没有设置idea的编码为utf-8从而导致xml的中文注释乱码，只要将idea的 ***\*File Encoding\**** 全设置为utf-8即可（记得maven先clean一下）。

当然为了避免之后的问题可以在里面把默认的编码也改为utf-8.





### 从 SqlSessionFactory 中获取 SqlSession

既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。例如：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
}
```

诚然，这种方式能够正常工作，对使用旧版本 MyBatis 的用户来说也比较熟悉。但现在有了一种更简洁的方式——使用和指定语句的参数和返回值相匹配的接口（比如 BlogMapper.class），现在你的代码不仅更清晰，更加类型安全，还不用担心可能出错的字符串字面值以及强制类型转换。

例如：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```





## CRUD

### namespace

namespace中的包名字要和 Dao/mapper 接口的包一致

### select

id：就是对应的namespace中的方法名
resultType：Sql语句执行的返回值
parameterType：参数类型



步骤：

1、编写接口

```java
package com.lian.dao;

import com.lian.Pojo.User;

import java.util.List;

public interface UserMapper {
    //查询全部用户
    List<User> getUserList();

    //根据id查询用户
    User getUserId(int id);

    //insert one user
    int addUser(User user);

    //update
    int updateUser(User user);

    //delete
    int deleteUser(int id);

}

```



2、编写对应mapper中的sql语句

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace=会绑定一个对应的Mapper接口-->
<mapper namespace="com.lian.dao.UserMapper">

    <!--select查询语句-->
    <select id="getUserList" resultType="com.lian.Pojo.User">
        select * from mybatis.user
    </select>

    <select id="getUserId" parameterType="int" resultType="com.lian.Pojo.User">
        select * from mybatis.user where id = #{id}
    </select>

    <!--对象中的属性，可以直接取出来-->
    <insert id="addUser" parameterType="com.lian.Pojo.User">
        insert into mybatis.user(id, name, pwd) VALUES(#{id}, #{name}, #{pwd})
    </insert>

    <update id="updateUser" parameterType="com.lian.Pojo.User">
        update mybatis.user
        set name = #{name}, pwd = #{pwd}
        where id = #{id};
    </update>
    
    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id = #{id}
    </delete>
</mapper>
```



3、测试

```java
package com.lian.dao;

import com.lian.Pojo.User;
import com.lian.utils.MyBatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

public class UserDaoTest {

    @Test
    public void test(){
        //第一步，获得sqlSession对象
        SqlSession sqlSession = MyBatisUtils.getsqlSession();

        //方法一：getMapper
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        List<User> userList = userMapper.getUserList();

        //方法二：不推荐
        //sqlSession.selectList("com.lian.dao.UserDao.getUserList");

        for (User user : userList) {
            System.out.println(user);
        }

        //关闭sqlsession
        sqlSession.close();
    }

    @Test
    public void getUserById(){
        SqlSession sqlSession = MyBatisUtils.getsqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        User user = mapper.getUserId(1);
        System.out.println(user);

        sqlSession.close();
    }

    //增删改需要提交事务
    @Test
    public void addUser(){
        SqlSession sqlSession = MyBatisUtils.getsqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        int res = mapper.addUser(new User(4, "llop", "12234"));
        if (res > 0 ){
            System.out.println("insert success!");
        }

        //提交事务
        sqlSession.commit();
        sqlSession.close();
    }

    //update
    @Test
    public void updateUser(){
        SqlSession sqlSession = MyBatisUtils.getsqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        int res = mapper.updateUser(new User(4, "llop-change", "12234000"));
        if (res > 0 ){
            System.out.println("update success!");
        }

        //提交事务
        sqlSession.commit();
        sqlSession.close();
    }

    //delete
    @Test
    public void deleteUser(){
        SqlSession sqlSession = MyBatisUtils.getsqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        int res = mapper.deleteUser(4);
        if (res > 0 ){
            System.out.println("delete success!");
        }
        //提交事务
        sqlSession.commit();
        sqlSession.close();
    }
}
```



### insert

### update

### delete

### 注意点，错误点

一定要提交事务

标签不要匹配错误

resource绑定mapper，需要使用路径`\`

程序配置文件必须符合规范

空指针异常：没有注册到资源

输出的xml文件中存在中文乱码问题

maven资源xml 等没有导出的问题



## 万能map

假设，我们的实体类，或者数据库中的表，字段或者参数过多,我们应当考虑使用Map！

野路子

```java
int addUser2(Map<String, Object> map);
```

```xml
<!--map-->
    <insert id="addUser2" parameterType="map">
        insert into mybatis.user(id, name, pwd) VALUES(#{userId}, #{userName}, #{password})
    </insert>
```

```java
@Test
    public void addUser2() {
        SqlSession sqlSession = MyBatisUtils.getsqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);

        Map<String, Object> map = new HashMap<>();
        map.put("userId", 5);
        map.put("userName", "oopos");
        map.put("password", "87654");

        int res = mapper.addUser2(map);
        if (res > 0) {
            System.out.println("insert success!");
        }

        //提交事务
        sqlSession.commit();
        sqlSession.close();
    }
```

Map传递参数，直接在sql中取出key即可	 [parameterType="map"]

对象传递参数，直接在sql中取对象的属性即可	[parameterType="Object"]

只有一个基本类型参数的情况下，可以直接在sql中取到

多个参数用Map,**或者注解**



## 模糊查询

1、java代码执行的时候，传递通配符`% %`

```java
List<User> userLike = mapper.getUserLike("%s%");
```

2、在sql拼接中使用通配符

```sql
select * from mybatis.user where name like "%"#{value}"%"
```

```sql
select * from mybatis.user where name like concat('%',#{value},'%')
```



## 配置解析

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中， 现实情况下有多种理由需要这么做。例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中使用相同的 SQL 映射

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

### **属性（properties）**

这些属性可以在外部进行配置，并可以进行动态替换。

编写一个配置文件`db.properties`

```xml
driver = com.mysql.cj.jdbc.Driver
url =jdbc:mysql://localhost/mybatis?serverTimezone=UTC&useUnicode=true&characterEncoding=utf8&useSSL=true
username = root
password = 123456
```

在核心配置文件中映入

```xml
<properties resource="org/mybatis/example/config.properties">
    <property name="username" value="dev_user"/>
    <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```

- 可以直接引入外部文件
- 还可以额外增加一些属性配置
- 如果两个文件同有一个字段，优先使用外部resource的字段

### 类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

1、

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

2、指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean

扫描实体类的包，他的默认别名就为这个类的类名，**首字母小写**，当然要是真的大写了也是不会错的。

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```



注：

在实体类比较少的时候，使用第一种方式。

如果实体类十分多，适合第二种方式

第一种可以自己设置别名，第二种不行但是可以加注解

优先级：`<typeAliases>` > 注解`@Alias("author")`>扫描包`<package name="domain.blog"/>`

`type="[JDBC|MANAGED]"`

### **数据源（dataSource）**

有三种内建的数据源类型（也就是 type="[UNPOOLED|POOLED|JNDI]"）



### 设置（settings）

| 设置名             | 描述                                                         | 有效值                                                       | 默认值 |
| :----------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----- |
| cacheEnabled       | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true   |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false                                                | false  |
| logImpl            | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |
|                    |                                                              |                                                              |        |



### 其他配置

#### [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)

#### [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)

#### [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)

一些插件



### 映射器（mappers）

1、【推荐】

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```
2、

```xml
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```

3、使用class

```XML
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```
注意点：

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下面



4、扫描包

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

注意点：

- 接口和他的Mapper配置文件必须同名
- 接口和他的Mapper配置文件必须在同一个包下面

练习：

- 数据库配置文件外部引入
- 实体类别名
- 保证UserMapper.xml和UserMapper接口名字一致，并且在同一个包下面（其实也可以在resourse中建立和类相同路径，这是结构底层原理）