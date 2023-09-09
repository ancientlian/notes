# docker mac 安装mysql

## 安装docker

略

## 设置 docker 镜像源

国内加速器服务

- 阿里云自己登录添加<https://cr.console.aliyun.com/cn-shanghai/instances/mirrors>
- 百度云加速
- 网易云加速

对于使用 macOS 的用户，在任务栏点击 Docker Desktop 应用图标 -> Perferences，在左侧导航菜单选择 Docker Engine，在右侧像下边一样编辑 json 文件。修改完成之后，点击 Apply & Restart 按钮，Docker 就会重启并应用配置的镜像地址了。

```text
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com",
    "https://docker.mirrors.ustc.edu.cn"
  ]
}
```

## 拉取镜像与启动容器

这里与 win 不同，mac m系列芯片为arm64 架构，所以要拉取 arm64 的mysql 镜像

```bash
docker pull arm64v8/mysql:latest
```

```bash
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d arm64v8/mysql:latest
```

–name：容器名，此处命名为mysql
-e：配置信息，此处配置mysql的root用户的登陆密码
-p：端口映射，此处映射 主机3306端口 到 容器的3306端口
-d：后台运行容器，保证在退出终端后容器继续运行

## 进入容易设置数据库远程访问

```bash
#进入容器
docker exec -it mysql bash
#登录mysql
mysql -u root -p
use mysql;
create user 'root'@'%' identified with mysql_native_password by 'root';
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```


## 挂载外卷

```java
--volume=/Users/lianzhiqiang/Documents/docker_vol/mysql/data:/var/lib/mysql --volume=/Users/lianzhiqiang/Documents/docker_vol/mysql/conf:/etc/mysql/my.cnf --volume=/var/lib/mysql 
```