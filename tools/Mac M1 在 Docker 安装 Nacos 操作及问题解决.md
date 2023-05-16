# Mac M1 在 Docker 安装 Nacos 操作及问题解决

## nacos 依赖 mysql

先安装mysql ，这里使用的是8+版本，原因在于原本的 5.7 版本中并没有对 m1 的良好支持，如果启动会有报错说查询不到对应版本信息（虽然可以通过自定义 mirror 实现）

mysql 配置参考（docker-compose）：

```Bash
  mysql:
    image: mysql:8.0
    container_name: mysql
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=root
    ports:
      - 3306:3306
    volumes:
      - ./mysql/data:/var/lib/mysql
      - ./mysql/my.cnf:/etc/mysql/my.cnf
      - ./mysql/initdb:/docker-entrypoint-initdb.d
      - ./mysql/mysql-files:/var/lib/mysql-files
```

## 安装 nacos

M1 支持版本nacos有限，这里要用`zhusaidong/nacos-server-m1:2.0.3`

```Bash
# 注意需要加版本号，否则会拉去失败
docker pull zhusaidong/nacos-server-m1:2.0.3

```

配置参考（docker-compose）：

```Bash
  nacos:
    image: zhusaidong/nacos-server-m1:2.0.3
    container_name: nacos
    restart: always
    depends_on:
      - mall4cloud-mysql
    ports:
      - 8848:8848
      - 9848:9848
      - 9849:9849
    environment:
      - JVM_XMS=256m
      - JVM_XMX=256m
      - MODE=standalone
      - PREFER_HOST_MODE=hostname
      - SPRING_DATASOURCE_PLATFORM=mysql
      - MYSQL_SERVICE_HOST=XXXX
      - MYSQL_SERVICE_DB_NAME=nacos
      - MYSQL_SERVICE_USER=root
      - MYSQL_SERVICE_PASSWORD=root
      - MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone==GMT%2B8
    volumes:
      - ./nacos/logs:/home/nacos/logs

```

## 问题

mac m1 中nacos 连接显示`No DataSource set`

```Bash
Nacos Server did not start because dumpservice bean construction failure : No DataSource set
```

mysql使用的是8+版本，所以记得添加`serverTimezone=GMT%2B8`

参考

```Bash
characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=false&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=GMT%2B8&nullCatalogMeansCurrent=true&allowPublicKeyRetrieval=true
```

## 重点

我首先在nacos 的容器中使用 curl 命令查看

```Bash
 curl localhost:3306
```

结果是：

```Bash
docker curl: (7) Failed to connect to localhost port 3306: Connection refused
```

顺着找下去原因

在容器外面是可以curl到容器的地址，但是就是在容量内不能curl连接到容器地址

这里直接使用电脑的 ip 代替 localhost 以及 127.0.0.1 来

附：mac 查找自己 ip 的cmd 命令

```Bash
ifconfig
```

将 ip 填到MYSQL_SERVICE_HOST的配置项中

最终成功问题解决
