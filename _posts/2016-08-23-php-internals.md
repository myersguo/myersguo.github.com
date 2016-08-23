---
layout: wp
title: PHP内核
---
在前面理解PHP的文件句柄[文章][phpnotes]中，我们已经从PHP源码中去寻找答案了。这篇文章，我将根据[THINK IN PHP INTERNALS][TPI]中的描述，做个简单的介绍，大致介绍下整个内核的架构，在最后，我们将修改PHP内核，更改LOG的输出，增加自己的输出中。   

### 准备 ###

>yum groupinstall 'Development Tools'   


### 源码目录 ###  

/根目录：包含一些说明文档及设计方案。重要的设计参考有:




- [phpnotes]: http://myersguo.github.io/2016/08/09/Understand-PHP-File-Description.html
- [TPI]: http://www.php-internals.com/book/
- [CODING_STANDARDS](https://github.com/php/php-src/blob/master/RCODING_STANDARDS)
- [README.GIT-RULES](/https://github.com/php/php-src/blob/master/RREADME.GIT-RULES)
- [README.MAILINGLIST_RULES](https://github.com/php/php-src/blob/master/RREADME.MAILINGLIST_RULES)
- [README.RELEASE_PROCESS](https://github.com/php/php-src/blob/master/README.RELEASE_PROCESS)
