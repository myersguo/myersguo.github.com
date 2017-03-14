---
layout: wp
title: php new features in 5.4 and 5.6
---

php5.4 2012年发布,php 5.6 2014年发布,php 7.0 2015年发布，我们来看一下他们的变迁。   

## PHP 5.4 ##

### 一些不兼容的更改 ###

>安全模式(Safe mode)不再被支持；  
>魔术方法(Magic quotes)不再支持；  
>移除了`register_globals`和`register_long_arrays`指令;   
>mbstring.script_encoding被zend.script-encoding取代;  
>引用传递(Call-time pass by reference)被弃用；  
>break和continue不再支持参数变量(eg break 1+foo()),但仍支持静态参数(eg: break 2;)  
>date.timezone必须初始化；   
>对字符串的offset将返回false,empty返回true,eg:$str='abc';isset($str['a'])返回false;  
>Array转化为string将报E_NOTICE,eg:`$a=['1','2'];$str = $a.'_test';`将返回'Array_test';   
>NULL,FALSE,empty string转化为object，并添加属性将产生E_WARNING,而非E_STRICT;   
>全局变量传入参数时，将产生500错误   
>Salsa10和Salsa20 hash算法被移除  
>两个空的array进行array_combine后返回array()而非FALSE  
>htmlentities同htmlspecialchars级别  


安全模式试图解决共享服务(shared-server）的安全问题（通过控制多用户时文件操作检查uid等其他手段，限制用户访问）;   
魔术方法，是对单引号，双音行，反斜线进行转义(加上反斜线);   
当`register_global`为`on`将对超级全局变量（EGPCS,Enriroment,GET,POST,Cookie,Server）中包含的变量注册为全局变量。($_SERVER['DOCUMENT_ROOT']将产生超级变量$DOCUMENT_ROOT)     
`register_long_arrays`指令告诉PHP是否预定义被弃用的`HTTP_*_VARS`预定义全局变量。   
引用传递，即使用`test(&$a)`会报错，直接`test($a)`即可。   


**新增关键字**    
  


* trait  
* callable  
* instead of   

### 新增特性 ###

* **trait**特性的支持   
* **array 短语法**的支持:
* 函数声明支持数组。   
* 匿名函数支持$this   
* <?= 默认支持  
* 类实例化可直接访问成员变量，eg (new Foo)->bar();  
* `Class::{expr}()`语法的支持（eg: Test::{ getVar()}();`将执行getVar返回的方法    
* 新增二进制直接量，例如：0b001001101   
* session扩展新增上传进度获取   
* 内置CLI模式HTTP SERVER，eg: php -S localhost:8080    

 
## PHP5.6 ##



> * 支持常量表达式(Constant expressions)   
> * 支持使用...表达可变参数(Variadic functions)   
> * 支持使用...传递其他参数   
> * 支持**进行幂运算   
> * use关键字可导入function和constant(以前只支持class,interface,namespace)   
> * phpdbg做为sapi被引入   
> * 对于htmlentities等字符相关函数使用default_charset作为默认字符集  
> * php://input可以重用  
> * 上传文件大小限制增大到2g  
> * gmp增加函数重载支持   
> * hash_equals避免时序攻击  
> * 增加__debugInfo方法   
> * ssl/tls功能升级   



常量表达式：`<?php CONST ONE = 1; CONST TWO = ONE *2;`   
PHP5.6之前可变参数函数使用`func_num_args`来处理，之后可使用...来表示其他参数    


###　废弃的方法 ###


`$HTTP_RAW_POST_DATA`将被废弃，应使用`php://input`代替.   
`iconv`和`mbstring`的编码设置被废弃，应该使用`default_charset`。   



