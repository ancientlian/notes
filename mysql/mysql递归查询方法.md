# 递归

## WITH RECURSIVE递归

```sql


```


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



