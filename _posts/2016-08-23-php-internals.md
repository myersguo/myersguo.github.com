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


####  编译PHP扩展  ####  

>php源码： ./configure --enable-opcache    
>percl: percl install apcu-4.0.2   
>php源码: ./config.nice --enable-apcu; ./configure --enable-apcu   
>phpize: phpize; ./configure --with-php-config=$php_dir/bin/php-config



###  php source code files ###


[词法分析](http://re2c.org/)：  
词法规则文件：   
Zend/zend_language_scanner.l   

语法解析:   
Zend/zend_language_parser.py   
Zend/zend_compiler.c   
Zend/zend_vm_opcodes.h  

编译并执行opcode代码执行：   
Zend/zend_vm_execute.h   


php处理逻辑：   
main/main.c    


Zend引擎处理逻辑:   
Zend/zend.c   
Zend/execute_API.c    

 
Zend全局变量定义：  
Zend/zend_globals.h   

Zend全局变量相关宏定义：  
Zend/zend_globals_macros.h   


PHP变量定义：   
main/php_variables.c   

SAPI处理逻辑:
main/SAPI.c   


内部函数定义(zend_version,strlen,define,error_reporting)等：   
Zend/zend_builtin_functions.c   


HTTP协议接口：   
main/rfc1867.c   


hashtable的定义:   

Zend/zend.h,
Zend/zend.c   

链表定义:  

Zend/zend_list.h  
Zend/zend_list.c  
Zend/zend_llist.h  
Zend/zend_llist.c   

常量定义：   

Zend/zend_constants.h   
Zend/zend_constants.c   


内存管理：  
Zend/zend_alloc.c   



### 扩展的加载流程 ###


PHP执行流程(启动，启动扩展，接受请求，关闭请求，关闭扩展)：     


```
sapi/cli/php_cli.c: 
sapi_startup ->  
cli_sapi_module.startup -> 
    php_module_startup ( /main/main.c)
    zend_startup ( zend/zend.c)
php_request_startup ->  

php_request_shutdown ->  
php_module_shutdown ->   
sapi_shutdown 
```

一个模块的入口，以CGI为例：   

```
static zend_module_entry cgi_module_entry = {
    STANDARD_MODULE_HEADER,
    "cgi-fcgi",
    NULL,
    PHP_MINIT(cgi),
    PHP_MSHUTDOWN(cgi),
    NULL,
    NULL,
    PHP_MINFO(cgi),
    NO_VERSION_YET,
    STANDARD_MODULE_PROPERTIES
};
```




php_module_startup,是PHP扩展的加载流程:   



```
#start up extensions staticly compiled in
php_register_internal_extensions_func(TSRMLS_C);
#start additional PHP extensions
php_register_extensions(&additional_modules, num_additional_modules TSRMLS_CC);
# load and startup extentions compiled as shared objects
php_ini_register_extnesions_TSRMLS_C);
zend_startup_modules(TSRMLS_C);
#start zend extensions
zend_startup_extensions();
#register additionnal functions
```

(可以直接使用`ext_skel`来生成扩展骨架)  

 以`json`扩展(module）的定义方法：     

1. 定义扩展：   


```
zend_module_entry foo_module_entry = {
#if ZEND_MODULE_API_NO >= 20010901
    STANDARD_MODULE_HEADER,
#endif
    "foo",                /* 扩展名,extension name */
    foo_functions,        /* 扩展函数列表,extension function list */
    NULL,                 /* extension-wide startup function */
    NULL,                 /* extension-wide shutdown function */
    PHP_RINIT(foo),       /* per-request startup function */
    PHP_RSHUTDOWN(foo),   /* per-request shutdown function */
    PHP_MINFO(foo),       /* information function */
#if ZEND_MODULE_API_NO >= 20010901
    FOO_VERSION,          /* extension version number (string) */
#endif
    STANDARD_MODULE_PROPERTIES
};
```

,比如`curl`扩展的模块定义:   

```
zend_module_entry json_module_entry = {
    STANDARD_MODULE_HEADER,
    "json",
    json_functions,
    PHP_MINIT(json),
    NULL,
    NULL,
    NULL,
    PHP_MINFO(json),
    PHP_JSON_VERSION,
    PHP_MODULE_GLOBALS(json),
    PHP_GINIT(json),
    NULL,
    NULL,
    STANDARD_MODULE_PROPERTIES_EX
};
```

2.  定义函数列表：  


```
static PHP_FUNCTION(json_encode);
```



```
static const function_entry json_functions[] = {
    PHP_FE(json_encode, arginfo_json_encode)
    PHP_FE(json_decode, arginfo_json_decode)
    PHP_FE(json_last_error, arginfo_json_last_error)
    PHP_FE_END
};
``` 


3. 实现函数列表   


参数信息：   

```
ZEND_BEGIN_ARG_INFO_EX(arginfo_json_encode, 0, 0, 1)
    ZEND_ARG_INFO(0, value)
    ZEND_ARG_INFO(0, options)
ZEND_END_ARG_INFO()
```


功能实现：   

```
static PHP_FUNCTION(json_encode)
{
    zval *parameter;
    smart_str buf = {0};
    long options = 0;

    if (zend_parse_parameters(ZEND_NUM_ARGS() TSRMLS_CC, "z|l", &parameter, &options) == FAILURE) {
        return;
    }

    JSON_G(error_code) = PHP_JSON_ERROR_NONE;

    php_json_encode(&buf, parameter, options TSRMLS_CC);

    ZVAL_STRINGL(return_value, buf.c, buf.len, 1);

    smart_str_free(&buf);
}
```

4. 编译   

1) 方法1:phpize

```
cd my_extension
/home/work/app/php/bin/phpize  
./configure --with-php-config=/home/work/app/php/bin/php-config
make
```

2) 方法2(php-src)   

./configure --prefix=/home/work/app/php --enable-my_extension

 

### 日志扩展 ###







参考资料：  
- [Cpp参考手册][cppref]  
- [kernel space definition][kernel_define]
- [phpinternalsbook](http://www.phpinternalsbook.com/build_system/building_php.html)



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

