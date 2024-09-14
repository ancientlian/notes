# 安装 redis

## 拉取镜像

```shell
docker search redis # 查找
docker pull redis # 拉取
docker images -a # 查看拉取
```

## 默认配置文件

配置文件下载地址：<https://redis.io/docs/management/config>
下载对应的版本

## 修改部分配置文件

1. 开启密码认证。搜索 `requirepass` 关键字，关掉注释，在关键字后面添加密码。如果公网不加密码验证则会被注入挖矿脚本, 可使用命令 `grep "requirepass" redis.conf`
2. 注释掉 `·`bind 127.0.0.1`，以允许其他 ip 连接
3. 关闭保护模式，将 yes 改为 no。允许其他机器连接。`protected-mode no`
4. daemonize 将 yes 设为 no；不然与 docker -d 冲突，导致容器启动失败
5. 设置容器内的日志位置：`logfile "/var/log/redis/log"`

## 容器命令

```shell
docker run \
 -d \
 -p 7936:6379 \
 --name redis \
 -v /home/lianzq/redis/data:/data \
 -v /home/lianzq/redis/redis.conf:/etc/redis.conf \
 redis:latest redis-server /etc/redis.conf
```

两个数据卷分别挂载持久化数据和配置；挂载配置文件后 redis 用指定配置文件运行，在命令最后加上了`redis-server /etc/redis.conf`
另外可以挂载 log 日志
`-v /home/lianzq/redis/redis.log:/var/log/redis.log \`，需要修改日志文件的写入权限

检查容器

> 输入对应指令：docker ps，查看容器是否创建成功。

检查日志

输入对应指令：`docker logs redis_6379`
进入 log 目录，输入`cat redis.log`,查看日志内容。

## 测试 redis

1. 输入指令：`docker exec -it redis_6379 bash`，进入容器内。
2. 输入指令：`redis-cli`，运行 redis 客户端。
3. 输入指令：`ping`，显示 PONG 代表测试成功！

## 开放防火墙端口

1. 输入指令：firewall-cmd --zone=public --add-port=6379/tcp --permanent，开放防火墙指定端口。
2. 输入指令：firewall-cmd --reload，重新加载防火墙。
