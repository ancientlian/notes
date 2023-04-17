# 常用

```SQL
-- 开始一个事务
BEGIN 
-- 事务回滚
ROLLBACK
-- 事务确认
COMMIT



```

更新多张表中的相同字段

```sql
UPDATE table1, table2
SET table1.field = 'new_value', table2.field = 'new_value'
WHERE table1.some_column = table2.some_column
```
