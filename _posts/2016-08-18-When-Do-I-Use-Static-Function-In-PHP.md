---
layout: wp
title: 什么时候使用静态成员函数
---

近期在项目中大量使用静态成员函数，作为service或者api的调用使用。为什么呢？什么场景下需要大量使用静态成员函数呢？来谈下：   

1. 工具类。  
工具类大多数使用OOP的形式，实际上成员函数是独立的存在。因此设计成静态成员，无需实例化对象，直接调用。   

2. API(或LIB)类。  
类似1，API接口（成员函数实现）一般不需要实例化类来实现，或者众多API接口与类的关联状态无法简单实例化（比如，对象属性众多，无法实例化）。这种情况多采用存储形式来保存对象属性，使用静态成员来实现API接口。   

3. 访问控制。   
使用静态成员实现单例模式。  

4. [性能提升](#perf)。  
If a method can be static, declare it static. Speed improvement is by a factor of 4.   




参考资料：   

[http://stackoverflow.com/questions/1257371/when-do-i-use-static-variables-functions-in-php](http://stackoverflow.com/questions/1257371/when-do-i-use-static-variables-functions-in-php)  
<span id="#perf">[http://www.mdproductions.ca/guides/50-best-practices-to-optimize-php-code-performance](http://www.mdproductions.ca/guides/50-best-practices-to-optimize-php-code-performance) </span> 



