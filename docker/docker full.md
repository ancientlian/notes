# m1 docker mysql8/arm64v8 解决 `only_full_group_by` 问题

## 问题原因

MySql从5.7版本开始默认开启only_full_group_by规则，规则核心原则如下，没有遵循原则的sql会被认为是不合法的sql

1. order by后面的列必须是在select后面存在的

2. select、having或order by后面存在的非聚合列必须全部在group by中存在

可以通过三种方式解决报错问题

- 第一种，修改sql使其遵守only_full_group_by规则

- 第二种，将MySql的版本降到5.7以下

- 第三种，关闭only_full_group_by规则

## 临时修改

sql 查询

```sql
SELECT @@GLOBAL.sql_mode;
SELECT @@SESSION.sql_mode;
```

在 sql 中执行

```sql
set @@sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

-- 或者
set @@GLOBAL.sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';
set @@SESSION.sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION';

```

## 永久配置文件修改

新建mysql 配置文件my.cnf或者修改原来的配置文件

```test
[mysqld]
# 其他随意

sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'

```

然后将配置文件拷贝到容器对应位置

```docker
docker cp ~/Documents/config/my.cnf container_name:/etc/mysql/
```

重启容器

```docker
docker restart 容器ID或容器名
```

## 备注

- Linux下：mysql配置文件在 /etc/my.cnf

- Windows下：mysql配置文件为 my.ini

- Docker版本的MySQL，配置文件在docker里/etc/mysql/my.cnf
