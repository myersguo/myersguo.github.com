---
layout: wp
title: curl error,params is empty
---

早起来公司，屁股刚坐稳，准备吃香蕉时，一个同事说线上的配置管理系统有个BUG。请求报403错误。   

>curl https://xxxx.com/get?p1=a&p2=b&p3=c   

结果提示403，我找到报错邮件，看到报错位置显示，p3这个参数为空或者验证不正确时，会403.   

那么p3是多少呢？我把p3加到报错信息里面，发现p3是空的。但我手动访问URL时，P3是有值的。那么错误就应该在curl时是错误的了。   

我将url加上引号：  

>curl 'https://xxxx.com/get?p1=a&p2=b&p3=c'   

结果OK了。   

what a lower bug...   

>curl -v -s  https://xxxx.com/get?p1=a&p2=b&p3=c       

结果就是：

```
*   Trying 54.243.217.10... connected
* Connected to xxxx.com (54.243.217.10) port 80 (#0)
> GET /get?p1=a HTTP/1.1
````

就传了个参数p1....    


这是为什么呢？    

挖个坑吧，以后看下curl源码解释下。。。



