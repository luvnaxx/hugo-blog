# 查询没有主键的MySQL表

MySQL查找所有数据库中没有主键的表

<!--more-->

------

```mysql
SELECT
	table_schema,
	table_name,
	count(
		CASE
		WHEN column_key IN ('PRI') THEN
			1
		ELSE
			NULL
		END
	) count
FROM
	information_schema. COLUMNS
WHERE
	table_schema NOT IN (
		'test',
		'sys',
		'mysql',
		'performance_schema',
		'information_schema',
		'test_slave'
	)
AND table_name NOT IN ('testtable', 'test')
GROUP BY
	table_schema,
	table_name
HAVING
	count = 0;
```

