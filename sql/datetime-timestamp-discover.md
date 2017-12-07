# 方法一：直接使用DATE_FORMAT
``` sql
SELECT
	COUNT(*),
	categoryid,
	`status`
FROM
	report
WHERE
	DATE_FORMAT(created, "%Y-%m") = '2017-05'
GROUP BY
	categoryid,
	`status`
```
时间: 0.194s
时间: 0.122s(datetime)
34440条

# 方法二：使用 >= 和 <= 取代 DATE_FORMAT
``` sql
SELECT
	*
FROM
	report
WHERE
	created >= '2017-05-01 00:00:00'
AND created <= '2017-05-31 23:59:59'
```

34440条

``` sql
SELECT
	COUNT(*),
	categoryid,
	`status`
FROM
	report
WHERE
	created >= '2017-05-01 00:00:00'
AND created <= '2017-05-31 23:59:59'
GROUP BY
	categoryid,
	`status`
```

时间: 0.213s
时间: 0.042s(datetime)

# 第三种方法使用id来划分。
``` sql
SELECT
	*
FROM
	report
WHERE
	id >= 160855
AND id <= 195293
```

需要做索引的是最快的

``` sql
SELECT
	COUNT(*),
	categoryid,
	`status`
FROM
	report
WHERE
	id >= 160855
AND id <= 195293
GROUP BY
	categoryid,
	`status`
```
0.022s

mysql时间类型无法索引

你对这个字段进行了运算，无法使用索引，查询某月，可以使用
where WorkDate  between 'YYYY-MM-01' and 'YYYY-MM-31'

# 第四种方法：
``` sql
SELECT
	COUNT(*),
	categoryid,
	`status`
FROM
	report
WHERE
	created BETWEEN '2017-05-01'
AND '2017-05-31'
GROUP BY
	categoryid,
	`status`
```
时间: 0.245s
时间: 0.040s（datetime）


# 第五种方法：左边不能有%，这样才可以使用索引。
```
SELECT
	COUNT(*),
	categoryid,
	`status`
FROM
	report
WHERE
	created LIKE '2017-05%'
GROUP BY
	categoryid,
	`status`
```

将`timestamp`转换成`datetime`之后
时间: 0.098s

# 第六种方法
之前想到使用索引记录每一天的id area，然后再通过 id来进行天与天之间的过滤。
后来干脆舍弃一点点性能，直接查找这个id。
查找天`（是否是过去的天?(缓存是否命中?读缓存:写缓存):直接找）`
