# MySQL服务器启动失败的问题

在启动mysql服务的时候失败了
使用`mysqld --console`这个命令查看一下日志信息，查询到如下的报错情况：

```
[ERROR] Can't start server: Invalid value for --default-authentication-plugin
```

于是知道了是之前的一个`my.ini`文件里面的配置有问题

```
default_authentication_plugin=mysql_native_password    
# 连接到服务器时使用的默认身份验证插件
# mysql 5.7:  mysql_native_password
# mysql 8: caching_sha2_password
```

注意安装的mysql对应的版本需要的插件是不一样的。

之后服务就能够正常启动了。