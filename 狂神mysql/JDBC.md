## 导包

8.0与5.0有差异



## 第一个JDBC程序

步骤总结:

1、加载驱动

2、连接数据库DriverManager

3、获得执行sq|的对象Statement

4、获得返回的结果集

5、释放连接

### DriverManager

```java
//1.加载驱动
//DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());
Class.forName("com.mysql.cj.jdbc.Driver");
```

使用`Class.forName()`写法在于`Driver`已经包含了`java.sql.DriverManager.registerDriver(new Driver())`

```java
/**
 * The Java SQL framework allows for multiple database drivers. Each driver should supply a class that implements the Driver interface
 * 
 * <p>
 * The DriverManager will try to load as many drivers as it can find and then for any given connection request, it will ask each driver in turn to try to
 * connect to the target URL.
 * 
 * <p>
 * It is strongly recommended that each Driver class should be small and standalone so that the Driver class can be loaded and queried without bringing in vast
 * quantities of supporting code.
 * 
 * <p>
 * When a Driver class is loaded, it should create an instance of itself and register it with the DriverManager. This means that a user can load and register a
 * driver by doing Class.forName("foo.bah.Driver")
 */
```

```java
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    //
    // Register ourselves with the DriverManager
    //
    static {
        try {
            java.sql.DriverManager.registerDriver(new Driver());
        } catch (SQLException E) {
            throw new RuntimeException("Can't register driver!");
        }
    }
```

### URL

```java
String url = "jdbc:mysql://localhost/school?" +
                "serverTimezone=UTC&" +
                "useUnicode=true&characterEncoding=utf8&useSSL=true";
```

### Connection

```java
Connection connection = DriverManager.getConnection(url, username, password);
```

Connection代表数据库，有很多的方法

```java
connection.rollback();//事务回滚
connection.commit();//事务提交
connection.setAutoCommit();//自动提交
```



### Statement

Statement执行SQL的对象

```java
connection.createStatement();
connection.prepareStatement();//执行SQL的对象
```

```java
statement.executeQuery();//查询操作返回ResultSet
statement.execute();//执行任何SQL
statement.executeUpdate();//更新，插入，删除都是。返回一个受影响的行数
```



### ResultSet

查询的结果集，封装了所有的查询结果

获得指定的数据类型

```java
resultSet.getObject();//在不知道列类型的情况下使用
//知道类型用指定的类型
resultSet.getInt();
resultSet.getString();
resultSet.getFloat();
resultSet.getDate();
```

遍历，指针

```java
resultSet.beforeFirst();//移动到最前面
resultSet.afterLast();//移动到最后面
resultSet.next();
resultSet.previous();//移动到前一行
resultSet.absolute(row);//移动到指定行
```



释放资源

```java
resultSet.close();
statement.close();
connection.close();
```



## statement对象





## SQL注入

sql存在漏洞

sql会被拼接`or`



## PreparedStatement

防止sql注入，并且效率更高。防SQL注入的本质，是把传递进来的参数当做字符；假设其中存在转义字符，比如说`符号会被直接转义



## 使用IDEA连接数据库

连接成功后可以选择数据库



更新完会后





## 事务

要么都成功，要么都失败

### ACID原则

原子性:要么全部完成，要么都不完成

一致性: 总数不变.

隔离性:多个进程互不干扰

持久性: 一旦提交不可逆，持久化到数据库了



隔离性的问题:

脏读: 一个事务读取了另-一个没有提交的事务

不可重复读:在同一个事务内,重复读取表中的数据，表数据发生了改变

虚读(幻读) : 在一个事务内，读取到了别人插入的数据，导致前后读出来结果不一致



代码实现：

1、开启事务`conn.setAutoCommit(false);`
2、一组业务执行完毕，提交事务
3、可以在catch语句中显示的定义回滚语句，但默认失败就会回滚

```java

```



## 数据库连接池

### DBCP

### C3P0

### Druid 阿里巴巴