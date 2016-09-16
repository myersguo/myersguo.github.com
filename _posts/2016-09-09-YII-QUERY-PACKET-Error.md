---
layout: wp
title:  Error while sending QUERY packet.
---

### 问题 ###

一个后台任务，使用pcntl多进程处理，mysql连接经常出现went away,Error while sending QUERY packet的报错。网上有说timeout超时的，需要更改max_allowed_packet的等等。   

但我之前没有遇到过啊。是不是多进程造成的？   

我们先看个测试程序：  

```
<?php
$db = new MySQLi('localhost','root','root');
if (pcntl_fork()) {
    $status = NULL;
    pcntl_wait($status);
    $result = $db->query('select version()');
} else {
    exit();
}

```
结果提示：

```
Warning: Error while sending QUERY packet. PID=12730 in /home/work/tmp/test2.php on line 9
MySQL server has gone away
```

为什么呢？   


### fork ###

首先我们要理解fork这个动作，我们先更改下代码看一下:

```
<?php

$db = new MySQLi('localhost','root','root');

echo 'start process my pid is '.getmypid().PHP_EOL;

for($i=0;$i<3;$i++) {
    if (pcntl_fork()) {
        echo 'i am parent '.$i.'my pid is '.getmypid().PHP_EOL;
        $status = NULL;
        pcntl_wait($status);
        $result = $db->query('select version()');
        #if ($db->error) echo $db->error;
    } else {
        echo 'i am child '.$i.'my pid is '.getmypid().PHP_EOL;
    }
    echo '---my pid is '.getmypid().PHP_EOL;

}   

```

结果输出:

```
start process my pid is 13096
i am parent 0my pid is 13096
i am child 0my pid is 13111
---my pid is 13111
i am parent 1my pid is 13111
i am child 1my pid is 13112
---my pid is 13112
i am parent 2my pid is 13112
i am child 2my pid is 13113
---my pid is 13113

Warning: Error while sending QUERY packet. PID=13112 in /home/work/tmp/test2.php on line 12
---my pid is 13112

Warning: Error while sending QUERY packet. PID=13111 in /home/work/tmp/test2.php on line 12
---my pid is 13111
i am parent 2my pid is 13111
i am child 2my pid is 13114
---my pid is 13114
---my pid is 13111

Warning: Error while sending QUERY packet. PID=13096 in /home/work/tmp/test2.php on line 12
---my pid is 13096
i am parent 1my pid is 13096
i am child 1my pid is 13115
---my pid is 13115
i am parent 2my pid is 13115
i am child 2my pid is 13116
---my pid is 13116
---my pid is 13115
---my pid is 13096
i am parent 2my pid is 13096
i am child 2my pid is 13117
---my pid is 13117
---my pid is 13096

```

从结果可以看出,fork之后的fork都是子进程才操作，即子进程接管了父进程的上下文。看图：  

![fork](http://www.csl.mtu.edu/cs4411.ck/www/NOTES/process/fork/fork-2.jpg)  

还是上面的代码，只有子进程exit之后才会继续父进程操作。fork是fork了一份进程上下文，堆栈信息，内存信息，文件描述符。

会到我们的问题。


参考资料：  
[http://www.electrictoolbox.com/mysql-connection-php-fork/](http://www.electrictoolbox.com/mysql-connection-php-fork/)  
[fork](http://www.csl.mtu.edu/cs4411.ck/www/NOTES/process/fork/create.html)  

