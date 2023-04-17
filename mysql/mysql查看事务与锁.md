# 常用指令

## 查看进程与 kill

```SQL
SHOW PROCESSLIST;
```

找到对应的 ID 之后 `KILL 41515;`

借助工具,Navicat 工具 -> 服务器监控 -> MySQL

## 查看锁

```SQL
show status like 'innodb_row_lock_%';
```

查询结果

```text
+-------------------------------+--------+
| Variable_name                 | Value  |
+-------------------------------+--------+
| Innodb_row_lock_current_waits | 1      |
| Innodb_row_lock_time          | 479764 |
| Innodb_row_lock_time_avg      | 39980  |
| Innodb_row_lock_time_max      | 51021  |
| Innodb_row_lock_waits         | 12     |
+-------------------------------+--------+
5 rows in set (0.00 sec)

解释如下：
Innodb_row_lock_current_waits : 当前等待锁的数量
Innodb_row_lock_time : 系统启动到现在，锁定的总时间长度
Innodb_row_lock_time_avg : 每次平均锁定的时间
Innodb_row_lock_time_max : 最长一次锁定时间
Innodb_row_lock_waits : 系统启动到现在总共锁定的次数  

```

查询是否锁表

```SQL
show OPEN TABLES where In_use > 0;
```

## 查看事务

当前运行的所有事务

```SQL
select * from information_schema.innodb_trx;
```

**注意:**
innodb_locks表在8.0.13版本中由`performance_schema.data_locks`表所代替；
innodb_lock_waits表则由`performance_schema.data_lock_waits`表代替。

```SQL
-- 当前出现的锁
select * from information_schema.innodb_locks;
-- 锁等待的对应关系 
select * from information_schema.innodb_lock_waits;

-- 8 版本后
select * from performance_schema.data_locks;
select * from performance_schema.data_lock_waits;
```
