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

