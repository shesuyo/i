# 常用命令

### 按照IP统计MYSQL连接数
``` sql
SELECT
	SUBSTRING_INDEX(HOST, ':', 1) AS ip,
	count(*) AS total
FROM
	information_schema. PROCESSLIST
GROUP BY
	ip;
```
