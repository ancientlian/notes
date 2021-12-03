## 1、mybatis层编写

编写MyBatis的核心配置文件

编写数据库对应的实体类

编写Dao层的 Mapper接口

编写接口对应的 Mapper.xml 文件

编写Service层的接口和实现类





## 2、spring层编写



```
问题
sqlSessionFactoryBeanName
sqlSessionTemplateBeanName
两者的区别
```

### Spring整合MyBatis

1、关联数据库文件

2、数据库连接池

3、配置SqlSessionFactory对象

4、配置扫描Dao接口包，动态实现Dao接口注入到spring容器中



### Spring整合service层





## 3、SpringMVC层

增加web框架支持







排错思路

问题：不存在







# 如果要添加一个新的功能

添加一个查询的按钮

1、首先进行前端的设置

```jsp
<div class="row">
    <%--新增书籍的按钮--%>
    <div class="col-md-4 column">
        <a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddBook">新增</a>
    </div>

    <%--空的占位--%>
    <div class="col-md-4 column"></div>

    <%--查询书籍--%>
    <div class="col-md-4 column">
        <form action="" method="" style="float: right" class="form-inline"><%--class="form-inline"使得按钮和输入框两个可以在同一行--%>
            <input type="text" name="queryBookName" class="form-control" placeholder="请输入要查询的书籍名称">
            <input type="submit" value="查询" class="btn btn-primary">
        </form>
    </div>
</div>
```

2、进行controller编写

```java

```

注意此时回去把前端没有设置好的action填写上

```jsp
<form action="${pageContext.request.contextPath}/book/queryBook" method="post" style="float: right" class="form-inline">
	<input type="text" name="queryBookName" class="form-control" placeholder="请输入要查询的书籍名称">
	<input type="submit" value="查询" class="btn btn-primary">
 </form>
```



从底层向上开始书写

1、dao层

1.1、BookMapper.java

```java
public interface BookMapper {
	……
    //查找书籍
    Books queryBookByName(@Param("bookName") String bookName);
	……
}
```

1.2、BookMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.example.dao.BookMapper">
    ……
	<select id="queryBookByName" resultType="Books">
        select * from ssmbuild.books where bookName = #{bookName}
    </select>
	……
</mapper>
```



2、service层

2.1、BookService.java

```java
public interface BookService {
	……
    //查找书籍
    Books queryBookByName(String bookName);
	……
}
```

2.2、BookServiceImpl.java

```java
public class BookServiceImpl implements BookService{

    private BookMapper bookMapper;

    public void setBookMapper(BookMapper bookMapper) {
        this.bookMapper = bookMapper;
    }
    
    ……
    @Override
    public Books queryBookByName(String bookName) {
        return bookMapper.queryBookByName(bookName);
    }
    ……
}
```



3、controller层

```java
@Controller
@RequestMapping("/book")
public class BookController {
    
    @Autowired
    @Qualifier("BookServiceImpl")
    private BookService bookService;

    //查询全部的书籍，并且返回到一个书籍展示页面
    @RequestMapping("/allBook")
    public String list(Model model){
        List<Books> list = bookService.queryAllBooks();
        model.addAttribute("list", list);
        return "allBook";

    }
    
    ……

    //查询书籍
    @RequestMapping("/queryBook")
    public String queryBook(String queryBookName, Model model){
        Books books = bookService.queryBookByName(queryBookName);
        List<Books> list = new ArrayList<Books>();
        list.add(books);
        model.addAttribute("list", list);
        return "allBook";
    }

}
```



后续：

对于查询可以稍微优化一下，如果查询为空那么显示全部的书籍，并且显示一个MULL提示

目前来说是已经查询一本书了，但是有一个问题就是，当查询的书名有多本同名的时候就会出错了。这里只要将原本单一的Books对象改为List就可以了。