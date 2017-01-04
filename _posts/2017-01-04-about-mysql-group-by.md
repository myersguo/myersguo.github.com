---
layout: wp
title: MySQL Handling of GROUP BY
---

先创建一个临时表：  


```
DROP TABLE IF EXISTS t1;
CREATE TABLE t1(
  name VARCHAR(255),
  add_time DATE
);
INSERT INTO t1 VALUES("myersguo","2017-01-04 10:00:00"),("myersguo","2017-01-04 11:00:00"),("guosen","2017-01-04 12:00:00"),("guosen","2017-01-04 13:00:00");
```

数据如下：  


```
 SELECT * FROM t1;
+----------+------------+
| name     | add_time   |
+----------+------------+
| myersguo | 2017-01-04 |
| myersguo | 2017-01-04 |
| guosen   | 2017-01-04 |
| guosen   | 2017-01-04 |
+----------+------------+
4 rows in set (0.00 sec)
```

我使用：   

```
SELECT * FROM t1 WHERE name="myersguo" GROUP BY name ORDER BY add_time DESC;
```

add_time永远返回第一个。order by 是没有效果的。那怎么办？   

```
SELECT * FROM (SELECT * FROM t1 WHERE name="myersguo" order by add_time) TEMP GROUP BY name;
```

嵌套SQL是没有问题的。   

那真的是这样吗？   

在[mysql官方文档](https://dev.mysql.com/doc/refman/5.7/en/group-by-handling.html)中提到：   

>If ONLY_FULL_GROUP_BY is disabled, a MySQL extension to the standard SQL use of GROUP BY permits the select list, HAVING condition, or ORDER BY list to refer to nonaggregated columns even if the columns are not functionally dependent on GROUP BY columns. This causes MySQL to accept the preceding query. In this case, the server is free to choose any value from each group, so unless they are the same, the values chosen are indeterminate, which is probably not what you want. Furthermore, the selection of values from each group cannot be influenced by adding an ORDER BY clause. Result set sorting occurs after values have been chosen, and ORDER BY does not affect which value within each group the server chooses. Disabling ONLY_FULL_GROUP_BY is useful primarily when you know that, due to some property of the data, all values in each nonaggregated column not named in the GROUP BY are the same for each group.   


不在group中的列，返回的值将是不可预测的。因此不管你的ORDER BY 如何，它返回的结果都是不影响它的返回的。但为什么我本机测试生效了呢？   


这个问题，只能留下了。  


参考资料：   

[https://dev.mysql.com/doc/refman/5.7/en/group-by-handling.html](https://dev.mysql.com/doc/refman/5.7/en/group-by-handling.html)   



