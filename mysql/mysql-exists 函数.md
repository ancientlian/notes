# exists

## 用法

```sql
SELECT
	a.* 
FROM
	orders a 
WHERE
	NOT EXISTS ( SELECT 1 FROM orders b WHERE b.orderId = a.orderId AND b.createTime > a.createTime )
--将orders表命名为a，下面一句又将orders表命名为b;可看做将orders表复制了两份，一份叫a,一份叫b
--常规写法是select b.orderId from ...，这里写成select 1 可以减少系统开销，提升运行效率

```

**将主查询的数据，放到子查询中做条件验证，根据验证结果（TRUE 或 FALSE）来决定主查询的数据结果是否得以保留。**
exists : 强调的是是否返回结果集，不要求知道返回什么, 只要exists引导的子句有结果集返回，那么exists这个条件就算成立了

not exists：只要not exists引导的子句无结果返回，那么此 not exists 条件成立，符合条件的该条数据得以保留

exists（sql 返回结果集为真）
not exists (sql 不返回结果集为真）

## `select 1`

select 1常搭配exists 当做条件使用，这里的select和 select 任何一列  、`select  *` 的功能相同。`select 1`中的1是常量（当然也可以写成`select 0`或`select 2`等等），`select 1 from table `得到的结果每一行都是1，结果行数与table行数相等。

因为和not exists搭配使用时，目地是判断是否有存在，所以不需要返回任何字段信息。写select 1比返回字段信息效率更高。


