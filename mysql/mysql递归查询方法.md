# 递归

## 根据子类ID查询所有父类

| id  | name | parent_id |
| --- | ---- | --------- |
| 1   | A    | 0         |
| 2   | B    | 1         |
| 3   | C    | 1         |
| 4   | D    | 2         |
| 5   | E    | 4         |
| 6   | F    | 1         |
| 7   | G    | 1         |

注：parent_id 为0表示顶级父节点，即A是顶级父辈，没有父节点

```sql
SELECT
	T2.id,
	T2.NAME 
FROM
	(
	SELECT
		@r AS _id,
		( SELECT @r := parent_id FROM demo WHERE id = _id ) AS parent_id,
		@l := @l + 1 AS lvl 
	FROM
		( SELECT @r := 5, @l := 0 ) vars,
		demo h 
	WHERE
		@r <> 0 
	) T1
	JOIN demo T2 ON T1._id = T2.id 
ORDER BY
	T1.lvl DESC

```

结果：

| id  | name |
| --- | ---- |
| 1   | A    |
| 2   | B    |
| 4   | D    |
| 5   | E    |

### 1、语句分析

```sql
SELECT
	@r AS _id,
	( SELECT @r := parent_id FROM demo WHERE id = _id ) AS parent_id,
	@l := @l + 1 AS lvl 
FROM
	( SELECT @r := 5, @l := 0 ) vars,
	demo h 
WHERE
	@r <> 0 
```

| \_id | parent_id | lvl |
| ---- | --------- | --- |
| 5    | 4         | 1   |
| 4    | 2         | 2   |
| 2    | 1         | 3   |
| 1    | 0         | 4   |

### 2、变量

`@l`只是用来标识节点的等级，底级子节点的lvl为1，父节点lvl值越大表示越靠近顶级父节点
`@r := 5`变量赋值语句，给变量@r赋值，值为5

### 3、变量的变化

```sql
@r AS _id,
(SELECT @r := parent_id FROM demo WHERE id = _id) AS parent_id
```

SQL语句在执行时会先执行From，即会先执行上一步的赋值操作，等同于

```sql
SELECT @r := parent_id FROM demo WHERE id = 5
```

| @r := parent_id |     |
| --------------- | --- |
| 4               |     | 

在查询Id为5的节点的父Id时，把这个父Id同时赋给了变量`@r`。因此`@r`值改变了，它从5变为了4。
当`@r`不等于0时，SQL语句会根据子ID向上查询父ID，又把父ID当做子ID赋值给`@r`，再次向上查询，直至`@r`变量的值为0为止。

## MySQL 执行顺序

```text
FROM
ON
JOIN
WHERE
GROUP By
CUBE|ROllUP
HAVING
SELECT
DISTINCT
ORDER BY
LIMIT
```

## WITH RECURSIVE递归 适用于**MySQL8.0**及以上版本


## 参考

[MYSQL递归查询，根据子类ID查询所有父类（宇宙第一详细教程）-阿里云开发者社区](https://developer.aliyun.com/article/1122623)
