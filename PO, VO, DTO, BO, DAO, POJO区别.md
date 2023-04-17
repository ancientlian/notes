
![fafeed42cc9748caa90288b06fe2b3c6.png](https://img-blog.csdnimg.cn/fafeed42cc9748caa90288b06fe2b3c6.png)

**DO（ Data Object）**：与数据库表结构一一对应，通过DAO层向上传输数据源对象。

**DTO（ Data Transfer Object）**：数据传输对象，Service或Manager向外传输的对象。

**BO（ Business Object）**：业务对象。 由Service层输出的封装业务逻辑的对象。

**AO（ Application Object）**：应用对象。 在Web层与Service层之间抽象的复用对象模型，极为贴近展示层，复用度不高。

**VO（ View Object）**：显示层对象，通常是Web向模板渲染引擎层传输的对象。

**POJO（ Plain Ordinary Java Object）**：在本手册中， POJO专指只有setter/getter/toString的简单类，包括DO/DTO/BO/VO等。

**Query**：数据查询对象，各层接收上层的查询请求。 注意超过2个参数的查询封装，禁止使用Map类来传输。


![](https://pic1.zhimg.com/v2-24e3ed681c02b6434681719753c53b40_r.jpg)



## 参考

[一篇文章讲清楚VO，BO，PO，DO，DTO的区别 - 知乎](https://zhuanlan.zhihu.com/p/102389552)
