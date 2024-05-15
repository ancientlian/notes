1. 拉取mysql镜像

第一个mysql进行外卷挂载之后，参数不需要密码

```bash
docker run --hostname=e66e46050fb2 --env=MYSQL_ALLOW_EMPTY_PASSWORD=yes --env=PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin --env=GOSU_VERSION=1.16 --env=MYSQL_MAJOR=8.0 --env=MYSQL_VERSION=8.0.34-1.el8 --env=MYSQL_SHELL_VERSION=8.0.34-1.el8 --volume=/Users/lianzhiqiang/Documents/dockertest/mysql/data:/var/lib/mysql --volume=/var/lib/mysql -p 3306:3306 -p 33060:33060 --runtime=runc -d mysql:latest
```
第二个设置root密码

```bash
docker run --hostname=22a299b58db8 --env=MYSQL_ROOT_PASSWORD=123456 --env=PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin --env=GOSU_VERSION=1.16 --env=MYSQL_MAJOR=8.0 --env=MYSQL_VERSION=8.0.34-1.el8 --env=MYSQL_SHELL_VERSION=8.0.34-1.el8 --volume=/Users/lianzhiqiang/Documents/dockertest/mysql/data:/var/lib/mysql --volume=/var/lib/mysql -p 3306:3306 -p 33060:33060 --runtime=runc -d mysql:latest
```
第三个设置root密码，user用户及密码

```bash
docker run --hostname=40b166eca62b --mac-address=02:42:ac:11:00:02 --env=MYSQL_USER=lian --env=MYSQL_PASSWORD=12345 --env=MYSQL_ROOT_PASSWORD=09876 --env=PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin --env=GOSU_VERSION=1.16 --env=MYSQL_MAJOR=8.0 --env=MYSQL_VERSION=8.0.34-1.el8 --env=MYSQL_SHELL_VERSION=8.0.34-1.el8 --volume=/Users/lianzhiqiang/Documents/dockertest/mysql/data:/var/lib/mysql --volume=/var/lib/mysql -p 3306:3306 -p 33060:33060 --runtime=runc -d mysql:latest
```
实际上，当第一次挂载之后，后面还挂载这个data，那么其实后面的user和password都不会生效


如果两个容器没有进行外卷挂载，那么两者的数据库数据是相互独立的



https://cloud.tencent.com/developer/article/1335633
https://www.cnblogs.com/haima/p/13394986.html