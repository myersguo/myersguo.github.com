---
title: PHP自动化测试框架
layout: wp
---

### PHP源码是怎么做测试的？ ###   

先举个例子，看PHP是怎么做测试的：  

设置环境变量：  
```
export TEST_PHP_EXECUTABLE=/home/work/app/php/bin/php 
```

执行测试：  

```
/home/work/app/php/bin/php  run-tests.php tests/basic/001.phpt 
```

结果：   

```
=====================================================================
PHP         : /home/work/app/php/bin/php 
PHP_SAPI    : cli
PHP_VERSION : 5.3.10
ZEND_VERSION: 2.3.0
PHP_OS      : Linux - Linux vagrant.localdomain 2.6.32-358.el6.x86_64 #1 SMP Fri Feb 22 00:31:26 UTC 2013 x86_64
INI actual  : /home/work/app/php/etc/php.ini
More .INIs  :  
CWD         : /home/work/data/www/php-src
Extra dirs  : 
VALGRIND    : Not used
=====================================================================
Running selected tests.
PASS Trivial "Hello World" test [tests/basic/001.phpt] 
=====================================================================
Number of tests :    1                 1
Tests skipped   :    0 (  0.0%) --------
Tests warned    :    0 (  0.0%) (  0.0%)
Tests failed    :    0 (  0.0%) (  0.0%)
Expected fail   :    0 (  0.0%) (  0.0%)
Tests passed    :    1 (100.0%) (100.0%)
---------------------------------------------------------------------
Time taken      :   11 seconds
=====================================================================
```

设置JUNIT输出：  

```
export TEST_PHP_JUNIT="/tmp/php.result"   
```

再次执行上述测试，查看php执行结果：  

```
cat /tmp/php.result
```

结果：  

```
<?xml version="1.0" encoding="UTF-8"?>
<testsuites>
<testsuite name="php-src" tests="1" failures="0" errors="0" skip="0" time="10.3658">
<testsuite name="php-src.tests.basic" tests="1" failures="0" errors="0" skip="0" time="10.3658">
<testcase classname='php-src.tests.basic' name='001.phpt - Trivial &quot;Hello World&quot; test' time='10.3658'>
</testcase>
</testsuite>
</testsuite>
</testsuites>
```

下面，我讲逐步讲解run-test.php源码(php源码版本5.3):     

检查：  

```
pcre扩展，
PHP_VERSION_ID全局变量(php 5.2.8才有);  
定义__DIR__(php5.3.0后才有); 
设置： timezone   
保存当前目录：CUR_DIR   
```

设置security related env:  

```
putenv('SSH_CLIENT=deleted');
```

设置：  

```
ini_set('pcre.backtrack_limit', PHP_INT_MAX);

```

清空缓存：  

```
while(@ob_end_clean());
```

检查环境变量 TEST_PHP_EXECUTABLE,TEST_PHP_CGI_EXECUTABLE：  

```
if (getenv('TEST_PHP_EXECUTABLE')) {
    $php = getenv('TEST_PHP_EXECUTABLE');
    if ($php=='auto') {
        $php = $cwd . '/sapi/cli/php';
        putenv("TEST_PHP_EXECUTABLE=$php");
        if (!getenv('TEST_PHP_CGI_EXECUTABLE')) {
            $php_cgi = $cwd . '/sapi/cgi/php-cgi';
            if (file_exists($php_cgi)) {
                putenv("TEST_PHP_CGI_EXECUTABLE=$php_cgi");
            } else {
                $php_cgi = null;
            }
        }
    }
    $environment['TEST_PHP_EXECUTABLE'] = $php;
}

#...测试是否可执行  

if (function_exists('is_executable') && !is_executable($php)) {
        error("invalid PHP executable specified by TEST_PHP_EXECUTABLE  = $php");
}
```

检查LOG的输出详情：  
```
if (getenv('TEST_PHP_LOG_FORMAT')) {
    $log_format = strtoupper(getenv('TEST_PHP_LOG_FORMAT'));
} else {
    $log_format = 'LEODS';
}
```

junit初始化：
```
junit_init();
```

write_information: 输出PHP当前信息,eg:  


```php
=====================================================================
PHP         : /home/work/app/php/bin/php 
PHP_SAPI    : cli
PHP_VERSION : 5.3.10
ZEND_VERSION: 2.3.0
PHP_OS      : Linux - Linux vagrant.localdomain 2.6.32-358.el6.x86_64 #1 SMP Fri Feb 22 00:31:26 UTC 2013 x86_64
INI actual  : /home/work/app/php/etc/php.ini
More .INIs  :  
CWD         : /home/work/data/www/php-src
Extra dirs  : 
VALGRIND    : Not used
=====================================================================
```




 



### 参考资料  ###  

[php ci jenkins](http://ci.qa.php.net/)    
[php write-test](http://qa.php.net/write-test.php)  
[php run-tests.php](https://github.com/php/php-src/blob/PHP-5.3/run-tests.php)  
[浅谈php官方自动化测试方法](https://www.ibm.com/developerworks/cn/opensource/os-cn-php-autotest/)  


