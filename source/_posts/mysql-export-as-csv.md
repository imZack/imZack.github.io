title: 'MySQL 匯出成 *.CSV'
date: 2014-07-14 22:16:40
tags:
	- mysql
---

要把 MySQL 倒出來...以前只會傻傻用 phpmyadmin 來做這檔事...(也好久以前了啦！

稍微記錄一下這次的內容方便未來使用

```sql
SELECT *
FROM tag_value_tbl
WHERE `acquire_time` >  '2014-03-01 00:00:00'
INTO OUTFILE '/tmp/meterdata.csv'
FIELDS TERMINATED BY '\t'
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
```

基本上就是設定 `INTO OUTFILE` `FIELDS TERMINATED BY` `ENCLOSED BY` `LINES TERMINATED BY` 這些變數，前面當然就是你要 select 那些東西塞進去檔案。