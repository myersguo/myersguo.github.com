---
layout: wp
title: Error while sending QUERY packet.
---

先说问题：   

今天遇到一个问题，执行脚本时，报错：   

```
PHP Error[2]: Error while sending QUERY packet. PID=2334
```

难道mysql传输的packet过大？   

```
SHOW VARIABLES LIKE 'max_allowed_packet';
```

显示：   

```
+--------------------+-----------+
| Variable_name      | Value     |
+--------------------+-----------+
| max_allowed_packet | 524288000 |
+--------------------+-----------+
```

不小啊。    

***是不是超时了啊？***   

做个测试：  

```
<?php
$connectionString = 'mysql:host=localhost;port=3306;dbname=test';
$username='root';
$password='root';
$db = new PDO($connectionString, $username, $password);
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
$sql = 'SELECT * FROM t1';
$statement=$db->prepare($sql);
$statement->execute();
$result=$statement->fetchAll();
var_dump($result);
```

这样是没有任何问题。那么我们改动一下,在execute之前加上：   

```
sleep(10);
```

再次执行，果然出现了：  

```
Warning: Error while sending QUERY packet. PID=5882 in /home/work/tmp/test.php on line 11
Fatal error: Uncaught exception 'PDOException' with message 'SQLSTATE[HY000]: General error: 2006 MySQL server has gone away' in /home/work/tmp/test.php:11
Stack trace:
#0 /home/work/tmp/test.php(11): PDOStatement->execute()
#1 {main}
  thrown in /home/work/tmp/test.php on line 11
```


为什么呢?我们捕获这个异常

```
...
sleep(10);
try{
$statement->execute();
}catch (PDOException $e){
    print "Error " .$e->getMessage() .PHP_EOL;                                                                                
}
...

```

结果显示：  

```
Error SQLSTATE[HY000]: General error: 2006 MySQL server has gone away
```

果然是超时自动断开了，我们看一下`mysql`的超时等待设置：  

```
SHOW GLOBAL VARIABLES LIKE 'wait_timeout'; 
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 10    |
+---------------+-------+
```

怎么办呢？能不能***自动重连***呢？   

我们需要写一个类，来管理这个连接,检查连接是否断开，断开了就重连：  

```
<?php

class PDOManager{
    private $db;
    private $connectionString;
    private $username;
    private $password;

    public function __construct($conn, $username, $password) 
    {
        $this->connectionString = $conn;
        $this->username = $username;
        $this->password = $password;
        $this->db = new PDO($conn, $username, $password);
    }
    
    public function getDb()
    {
        if($this->db === null || !$this->ping())
            $this->reconnect();
        return $this->db;
    } 
    public function ping()
    {
        try{
            $this->db->query("SELECT 1");
        }catch(PDOException $e) {
            print "Error " .$e->getMessage() .PHP_EOL;
            return false; 
        }
        return true;
    }
    public function reconnect()
    {
        print "Error Reconect " .PHP_EOL;
        $this->db = new PDO($this->connectionString, $this->username, $this->password);
    }

    public function __call($name, $parameters)
    {
        return call_user_func_array(array($this->getDb(),$name), $parameters);
    }

}

$connectionString = 'mysql:host=localhost;port=3306;dbname=test';
$username='root';
$password='root';
$db = new PDOManager($connectionString, $username, $password);
$db->getDb()->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
$sql = 'SELECT * FROM t1';
sleep(10);
try{
$statement=$db->getDb()->prepare($sql);
$statement->execute();
}catch (PDOException $e){
    print "Error " .$e->getMessage() .PHP_EOL;
}
$result=$statement->fetchAll();
var_dump($result);
```

这样，会有一个问题，因为每次连接都要`SELECT一下，判断是否可以连接`，这样的成本是否太高呢？    


### mysql 索引优化 ###

什么情况下创建索引呢？一般记录数超过一定数量，并且选择性较多（对于性别、类型等字段没必要创建索引）。




附录：   

1. mysql start method:   

mysqld: mysql server,mysql的服务器程序，可以man mysqld查看详情;  
mysqld_safe: mysql startup script,mysql的shell脚本，用来启动mysqld;    
    比如，我的启动程序命令就是：  

```
/bin/sh /home/work/app/mysql/bin/mysqld_safe --datadir=/home/work/app/mysql/data --pid-file=/home/work/app/mysql/data/vagrant.localdomain.pid --user=root
```

 
myslql.server: mysql startup script;  
mysqld_multi: manage multiple mysql servers;   

### 参考资料 ###

[http://www.ywnds.com/?p=8378](http://www.ywnds.com/?p=8378)   


