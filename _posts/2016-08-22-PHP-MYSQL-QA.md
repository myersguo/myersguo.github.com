---
layout: wp
title: php,mysql连接
---
在如何使用php连接mysql的官方文档上，写着，推荐使用mysqli和pdo的扩展，不推荐使用mysql（在7.A版本中已经被移除）。看一下三个对比的表格：  

<table>
<thead>
<tr><td>mysqli</td><td>pdo</td><td>mysql</td></tr>
</thead>
<tbody>
<tr><td>
php 5.0版本;面向对象；面向过程；
</td><td>PHP5.1版本;面向对象；</td><td>PHP2.0版本;面向过程</td></tr>
</tbody>
</table>


PDO的方式，不仅仅提供MYSQL的扩展，PDO作为数据驱动层提供了统一的数据封装，因此你不必关系底层的数据连接对象是什么就可以使用。PDO的数据层扩展有很多：  
pdo_mysql  
pdo_oci  
pdo_odbc  
pdo_pgsql  
pdo_sqlite   



参考资料：  
[1][Choosing an API](http://php.net/manual/zh/mysqlinfo.api.choosing.php)  
[2][PHP PDO扩展源码](https://github.com/php/php-src/tree/master/ext/pdo)    
[3][PHP MYSQLI扩展源码](https://github.com/php/php-src/tree/master/ext/mysqli)  
