# my.cnf

基础配置

```ini
[mysqld]
# 数据库服务器的主机名
bind-address = 127.0.0.1
# 数据库服务器的端口号
port = 3306
# 数据库文件存储的位置
datadir = /var/lib/mysql
# 错误日志文件的位置
log-error = /var/log/mysql/error.log
# 慢查询日志文件的位置
slow-query-log-file = /var/log/mysql/slow.log
# 最大连接数
max_connections = 1000
# 查询缓存大小
query_cache_size = 64M
# 表缓存大小
table_open_cache = 2000
# 临时表大小限制
tmp_table_size = 64M
# 最大连接数的超时时间
wait_timeout = 600
# 读取超时时间
interactive_timeout = 600
# 默认字符集
character-set-server = utf8mb4
# 事务隔离级别
transaction-isolation = READ-COMMITTED
# 启用慢查询日志
slow-query-log = 1
# 启用查询缓存
query_cache_type = 1
# 禁用外键约束
innodb_flush_log_at_trx_commit = 0
# InnoDB日志文件的大小
innodb_log_file_size = 256M
# InnoDB缓冲池大小
innodb_buffer_pool_size = 1G

```
