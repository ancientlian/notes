
```yml
version: "3"
services:

   db:
     image: mysql:8.0
     command:
      - --default_authentication_plugin=mysql_native_password
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci     
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
  db_data:
```


## yml配置指令参考

### version 

指定本 yml 依从的 compose 哪个版本制定的

### build

指定为构建镜像上下文路径：

### command

覆盖容器启动的默认命令。
```yml
command: ["bundle", "exec", "thin", "-p", "3000"]
```

### container_name

指定自定义容器名称，而不是生成的默认名称。

### depends_on

设置依赖关系。


### environment

添加环境变量。可以使用数组或字典、任何布尔值，布尔值需要用引号引起来，以确保 YML 解析器不会将其转换为 True 或 False。
```yml
environment:
  RACK_ENV: development
  SHOW: 'true'
```


### restart

- no：是默认的重启策略，在任何情况下都不会重启容器。
- always：容器总是重新启动。
- on-failure：在容器非正常退出时（退出状态非0），才会重启容器。
- unless-stopped：在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器

```yml
restart: "no"
restart: always
restart: on-failure
restart: unless-stopped
```

### volumes

将主机的数据卷或着文件挂载到容器里。可以设置为宿主机路径(`HOST:CONTAINER`)或者数据卷名称(`VOLUME:CONTAINER`)，并且可以设置访问模式 （`HOST:CONTAINER:ro`）,路径支持相对路径。

```yml
version: "3.7"
services:
  db:
    image: postgres:latest
    volumes:
      - "/localhost/postgres.sock:/var/run/postgres/postgres.sock"
      - "/localhost/data:/var/lib/postgresql/data"
```



### 使用变量、读取变量

Compose 模板文件支持动态读取主机的系统环境变量和当前目录下的 `.env` 文件中的变量。
```yml
version: "3"
services:

db:
  image: "mongo:${MONGO_VERSION}"
```

若当前目录存在 `.env` 文件，执行 `docker-compose` 命令时将从该文件中读取变量。

在当前目录新建 `.env` 文件并写入以下内容。

```text
# 支持 # 号注释
MONGO_VERSION=3.6
```
此时执行 `docker-compose up` 则会启动一个 `mongo:3.6` 镜像的容器。

[Compose 模板文件 - Docker — 从入门到实践](https://yeasy.gitbook.io/docker_practice/compose/compose_file)
