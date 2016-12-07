---
layout: wp
title: PHP内核
---
在前面理解PHP的文件句柄[文章][phpnotes]中，我们已经从PHP源码中去寻找答案了。这篇文章，我将根据[THINK IN PHP INTERNALS][TPI]中的描述，做个简单的介绍，大致介绍下整个内核的架构，在最后，我们将修改PHP内核，更改LOG的输出，增加自己的输出中。   

### 准备 ###

>yum groupinstall 'Development Tools'   


### 源码目录 ###  

/根目录：包含一些说明文档及设计方案。重要的设计参考有:[PHP设计文档][[CODING_STANDARDS]等；
/build: PHP的编译脚本  
/ext: PHP的官方扩展目录，eg: standard,json,mysqli等;  
/main: PHP的核心基础库  
/Zend: Zend引擎，脚本的语法解析，opcode的执行及实现；  
/pear: Php的官方代码仓库[pear][pear]入口  
/sapi: php cgi(apache2handler, cgi,cli,fpm,litespeed)的实现代码  
/TSRM: Thread Safe Resource Manager,PHP的线程安全管理实现  
/tests: Php的测试脚本集合  
/win32: win平台下的实现，如：socket, syslog等。  
/[run-tests.php][runphptest]: 执行/tests下的测试文件，并发送测试报告(后续，我们将详细介绍PHP的测试框架)  


### 编译PHP ###   

```
sudo yum install autoconf213 -y 
export PHP_AUTOCONF=/usr/bin/autoconf-2.13
./configure --prefix=/tmp/php_src



###  understand php code line by line ###



参考资料：  
- [Cpp参考手册][cppref]  
- [kernel space definition][kernel_define]


[phpnotes]: http://myersguo.github.io/2016/08/09/Understand-PHP-File-Description.html
[TPI]: http://www.php-internals.com/book/
[CODING_STANDARDS]: https://github.com/php/php-src/blob/master/RCODING_STANDARDS
[README.GIT-RULES]: https://github.com/php/php-src/blob/master/RREADME.GIT-RULES
[README.MAILINGLIST_RULES]: https://github.com/php/php-src/blob/master/RREADME.MAILINGLIST_RULES
[README.RELEASE_PROCESS]: https://github.com/php/php-src/blob/master/README.RELEASE_PROCESS
[pear]: https://pear.php.net/index.php  
[runphptest]: https://github.com/php/php-src/blob/master/run-tests.php  
[cppref]: http://zh.cppreference.com/w/%E9%A6%96%E9%A1%B5  
[kernel_define]: http://www.linfo.org/kernel_space.html   

