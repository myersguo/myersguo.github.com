---
layout: wp
title: mysql index
---

问题：一个 student 表，有100 万条记录，假如 status = 0 的有90万，剩下的 status >0 的10 万，每个人都有一个属性 school, 现在要查询， school 为空的， status>0 的数据。   

`select count(id) from student where school = '' and status`  
我们发现，mysql 使用 school 作为索引，没有使用 status，这就造成了需要从 90 万查找数据。如果使用 status 则只需要从 10 万种查找数据了。怎么优化，如果这是一种常态，可以使用嵌套查询.  
```
select count(id) from (select id, status) from student where status>0) as c where school = '';

```
或者使用 force index`FORCE INDEX('idx_student_status')`   
```


### 索引的结构 ###

索引内容包含两部分。 索引值 + 块地址。     

` the clustered index is synonymous with the primary key. ` 主键 key 就是聚集索引。

![https://www.geeksforgeeks.org/wp-content/uploads/gq/2016/07/indexing3.png](https://www.geeksforgeeks.org/wp-content/uploads/gq/2016/07/indexing3.png)




### 参考 ###

[https://dev.mysql.com/doc/refman/5.7/en/innodb-index-types.html](https://dev.mysql.com/doc/refman/5.7/en/innodb-index-types.html)   
[https://github.com/jeremycole/innodb_diagrams/blob/master/images/InnoDB_Structures.pdf](https://github.com/jeremycole/innodb_diagrams/blob/master/images/InnoDB_Structures.pdf)  

[https://www.geeksforgeeks.org/indexing-in-databases-set-1/](https://www.geeksforgeeks.org/indexing-in-databases-set-1/)   
[https://monkeysayhi.github.io/2018/03/06/%E6%B5%85%E8%B0%88MySQL%E7%9A%84B%E6%A0%91%E7%B4%A2%E5%BC%95%E4%B8%8E%E7%B4%A2%E5%BC%95%E4%BC%98%E5%8C%96/](https://monkeysayhi.github.io/2018/03/06/%E6%B5%85%E8%B0%88MySQL%E7%9A%84B%E6%A0%91%E7%B4%A2%E5%BC%95%E4%B8%8E%E7%B4%A2%E5%BC%95%E4%BC%98%E5%8C%96/)  
[https://blog.jcole.us/innodb/](https://blog.jcole.us/innodb/)   
[https://www.cnblogs.com/leefreeman/p/8315844.html](https://www.cnblogs.com/leefreeman/p/8315844.html)   

