伪ajax，就是使用iframe



# \<url-pattern>/\</url-pattern>和\<url-pattern>/*\</url-pattern>的区别

```

其中/和/*的区别：
< url-pattern > / </ url-pattern >  不会匹配到*.jsp，即：*.jsp不会进入spring的 DispatcherServlet类 。
< url-pattern > /* </ url-pattern > 会匹配*.jsp，会出现返回jsp视图时再次进入spring的DispatcherServlet 类，导致找不到对应的controller所以报404错。

总之，关于web.xml的url映射的小知识:
< url-pattern>/</url-pattern>  会匹配到/login这样的路径型url，不会匹配到模式为*.jsp这样的后缀型url
< url-pattern>/*</url-pattern> 会匹配所有url：路径型的和后缀型的url(包括/login,*.jsp,*.js和*.html等)

```



前端技术栈

js：

- 函数：闭包
- Dom
  - id，name，tag
  - create，remove
- Bom
  - window
  - document



ES6规范：import require





# ajax验证用户名体验

