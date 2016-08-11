---
layout: wp
title: PHP中后期绑定：self,parent,static
---

先看一个PHP官方的例子：   

```php

<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        self::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();
?>
```

上面的结果将输出: A  

静态变量的生命周期存在于整个PHP脚本的线程周期，静态属性用self来引用。self指定当前的类的静态成员变量。PHP使用static 关键词实现后期绑定，即static即在第一次运行后进行绑定。在以后的执行中使用第一次绑定的结果，即不允许使用static多次绑定。   



TODO:PHP源码对static的实现   


附：  
PHP的进程数查看：
pstree or ps axf   
查看PHP进程的负载：   
top -p xxx  
ps -eLf  
gdb -p 
info threads


参考：  

1. http://php.net/manual/en/language.oop5.late-static-bindings.php    
2. http://php.net/manual/en/language.oop5.static.php   
3. PHP 解析过程：http://www.laruence.com/2008/08/11/147.html   
