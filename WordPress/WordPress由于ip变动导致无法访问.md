# WordPress 由于 ip 变动导致无法访问（一直访问历史 ip）

连接数据库

```shell

mysql -uroot -h 你服务器的ip -P 数据库所在容器的端口号 -p 密码
use wordpress;

select * from wp_options limit 1
update wp_options set option_value="这里填新IP:端口或域名" where option_name='siteurl'
```

输入管理员帐号密码进入控制台，在“设置-常规选项-站点地址”中将正确的 URL 输入，例如`http://114.514.191.810:1145`
