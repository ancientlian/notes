语法：`CAST (expression AS data_type)`

1、SELECT CAST( ’2012-12-24′ AS DATE ) 把字符串转换成date类型
2、SELECT CAST( 2012 AS string ) 把数字转化成字符串
3、SELECT CAST( ‘baiduzhidao’ AS char(3) ) 取指定长度的字符

二进制，同带binary前缀的效果 : BINARY
字符型，可带参数 : CHAR() 
日期 : DATE 
时间: TIME 
日期时间型 : DATETIME 
浮点数 : DECIMAL 
整数 : SIGNED 
无符号整数 : UNSIGNED 




1.hql的cast 转换的类型不是数据库中的类型而是java中的(int, String...)
2.如上代码 类型名写为String会报错，改为string才OK，估计是必须写为小写。

```sql
CAST(value as type);  
CONVERT(value, type);
```

