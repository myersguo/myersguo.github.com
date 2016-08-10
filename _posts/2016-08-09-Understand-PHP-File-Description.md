---
layout: wp
title: 理解PHP的文件句柄
---

PHP标准库(standard)的一个函数file_get_contents的第一个参数是string $filename,这个文件名可以是本地文件，也可以是远程文件(http://,ftp://),也可以是标准文件流(php://)。要想使用远程文件，需要在php.ini中开启allow_url_fopen选项。为什么可以这样呢？从PHP文件源码(以5.3版本为例)中，一探究竟：   

在php-src/ext/standard/file.c中找到file_get_contents的内容：  

```
stream = php_stream_open_wrapper_ex(filename, mode, ((flags & PHP_FILE_USE_INCLUDE_PATH) ? USE_PATH : 0) | ENFORCE_SAFE_MODE | REPORT_ERRORS, NULL, context);

```
继续查看php_stream_open_wrapper_ex:  

```
#define php_stream_open_wrapper_ex(path, mode, options, opened, context)    _php_stream_open_wrapper_ex((path), (mode), (options), (opened), (context) STREAMS_CC TSRMLS_CC)

```

追查php-src/main/streams/streams.c:  

```
wrapper = php_stream_locate_url_wrapper(path, &path_to_open, options TSRMLS_CC);
..
    php_stream_locate_url_wrapper:

    for (p = path; isalnum((int)*p) || *p == '+' || *p == '-' || *p == '.'; p++) {
        n++;
    }

    if ((*p == ':') && (n > 1) && (!strncmp("//", p+1, 2) || (n == 4 && !memcmp("data:", path, 5)))) {
        protocol = path;
    } else if (n == 5 && strncasecmp(path, "zlib:", 5) == 0) {
        /* BC with older php scripts and zlib wrapper */
        protocol = "compress.zlib";
        n = 13;
        php_error_docref(NULL TSRMLS_CC, E_WARNING, "Use of \"zlib:\" wrapper is deprecated; please use \"compress.zlib://\" instead");
    }
    if ((*p == ':') && (n > 1) && (!strncmp("//", p+1, 2) || (n == 4 && !memcmp("data:", path, 5)))) {
        protocol = path;
    } 
    ...
    if (protocol) {
        char *tmp = estrndup(protocol, n);
        if (FAILURE == zend_hash_find(wrapper_hash, (char*)tmp, n + 1, (void**)&wrapperpp)) {
            php_strtolower(tmp, n);

`` 
可见在打开文件的时候，对path做了解析，找到path对应的protocol，然后zend_hash_find找到对应的函数处理读操作。   


附：  


尝试strace我们来查看下file_get_contents的执行过程：  
test.php如下：   
file_get_contents('http://myersguo.github.io/');  

strace php test.php  

尝试通过gdb来查看执行过程：   

>gdb php test.php  
>start  
>s 10000
>bt


    #0  zend_register_constant (c=0x7fffffffdba0) at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend_constants.c:414
    #1  0x00000000007b4b2d in zend_register_long_constant (name=<value optimized out>, name_len=28, lval=<value optimized out>, flags=<value optimized out>, module_number=0)
    at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend_constants.c:187
    #2  0x00000000007b4d3d in zend_register_standard_constants () at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend_constants.c:117
    #3  0x00000000007c2e8b in zend_startup (utility_functions=<value optimized out>, extensions=<value optimized out>) at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend.c:697
    #4  0x0000000000770305 in php_module_startup (sf=0xfb3da0, additional_modules=0x0, num_additional_modules=0) at /home/data/rpmbuild/BUILD/php-5.3.10/main/main.c:1888
    #5  0x00000000008494ad in php_cli_startup (sapi_module=<value optimized out>) at /home/data/rpmbuild/BUILD/php-5.3.10/sapi/cli/php_cli.c:398
    #6  0x0000000000849d5b in main (argc=1, argv=0x7fffffffe218) at /home/data/rpmbuild/BUILD/php-5.3.10/sapi/cli/php_cli.c:770


刚好像走弯路了,重新debug:  

>start  
>break php_stream_locate_url_wrapper
>c

````
#0  0x0000003d4f6db60d in read () from /lib64/libc.so.6
#1  0x0000003d4f671f68 in _IO_new_file_underflow () from /lib64/libc.so.6
#2  0x0000003d4f671908 in _IO_file_xsgetn_internal () from /lib64/libc.so.6
#3  0x0000003d4f6676e2 in fread () from /lib64/libc.so.6
#4  0x00000000007d683d in zend_stream_getc (file_handle=0x7fffffffdfa0, buf=0x13517b3 "\001", len=4093) at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend_stream.c:148
#5  zend_stream_read (file_handle=0x7fffffffdfa0, buf=0x13517b3 "\001", len=4093) at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend_stream.c:168
#6  0x00000000007d6c7b in zend_stream_fixup (file_handle=0x7fffffffdfa0, buf=0x7fffffffb718, len=0x7fffffffb710) at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend_stream.c:262
#7  0x000000000079d6e8 in open_file_for_scanning (file_handle=0x7fffffffdfa0) at Zend/zend_language_scanner.l:261
#8  0x000000000079e2c9 in compile_file (file_handle=0x7fffffffdfa0, type=8) at Zend/zend_language_scanner.l:352
#9  0x000000000060078a in phar_compile_file (file_handle=0x7fffffffdfa0, type=8) at /home/data/rpmbuild/BUILD/php-5.3.10/ext/phar/phar.c:3393
#10 0x00000000007c0df6 in zend_execute_scripts (type=8, retval=0x0, file_count=3) at /home/data/rpmbuild/BUILD/php-5.3.10/Zend/zend.c:1228
#11 0x000000000076efcd in php_execute_script (primary_file=0x7fffffffdfa0) at /home/data/rpmbuild/BUILD/php-5.3.10/main/main.c:2308
#12 0x000000000084ac35 in main (argc=1, argv=0x7fffffffe218) at /home/data/rpmbuild/BUILD/php-5.3.10/sapi/cli/php_cli.c:1184
```

没找到合适的DEBUG方式，HOW TO DEBUG PHP FILES？这个下次详细再讲吧。  

