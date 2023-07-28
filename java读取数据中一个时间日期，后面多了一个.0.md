mysql中存储的日期时间是**2021-04-22 16:48:36**

查出来以后显示：**2021-04-22 16:48:36.0**

其实后面这个.0是纳秒

解决方法：查询的时候使用`DATE_FORMAT()`格式化

```sql
DATE_FORMAT(request_date,'%Y-%m-%d %H:%i:%s') requestDate
```
