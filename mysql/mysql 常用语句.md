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

## 连接语句

 将多个表的字段进行连接，可以指定连接条件。
```sql
-- 内连接(inner join)
    - 默认就是内连接，可省略inner。
    - 只有数据存在时才能发送连接。即连接结果不能出现空行。
    on 表示连接条件。其条件表达式与where类似。也可以省略条件（表示条件永远为真）
    也可用where表示连接条件。
    还有 using, 但需字段名相同。 using(字段名)
-- 交叉连接 cross join
        即，没有条件的内连接。
        select * from tb1 cross join tb2;
-- 外连接(outer join)
    - 如果数据不存在，也会出现在连接结果中。
    -- 左外连接 left join
        如果数据不存在，左表记录会出现，而右表为null填充
    -- 右外连接 right join
        如果数据不存在，右表记录会出现，而左表为null填充
-- 自然连接(natural join)
    自动判断连接条件完成连接。
    相当于省略了using，会自动查找相同字段名。
    natural join
    natural left join
    natural right join
select info.id, info.name, info.stu_num, extra_info.hobby, extra_info.sex from info, extra_info where info.stu_num = extra_info.stu_id;

```


## 联表update

```sql
BEGIN;
	UPDATE t_contract_info c
	INNER JOIN t_bill_info b ON b.contractNo = c.contractNo AND b.billSubType = '押金' 
	SET c.deposit = b.billAmount,
	b.billPaidAmount = b.billAmount,
	b.billStatus = '1' 
	WHERE
	c.contractNo = 'ABC123';
COMMIT;
```

