---
layout: wp
title: 压力测试工具 vegeta
---

介绍一下用`golang`写的压力测试工具[vegeta](https://github.com/tsenart/vegeta)（发音：喂，吉他），还是很强大的。   

如果不需要作为lib库使用，直接下载二进制文件即可.[点击下载](https://github.com/tsenart/vegeta/releases)     

### 直接使用 ###


```
echo "GET https://github.com" |./vegeta attack -duration=10s -rate=100 >results.bin
```



###查看结果###

   

```
./vegeta report -inputs=results.bin -reporter=json 
```   

或者：   

```
./vegeta report -inputs=results.bin 
```

生成测试报告:   

```
./vegeta report -inputs=results.bin --reporter=plot  > plot.html
```   


### 增加HTTP头 ###



假如有需要登录的操作，可提前在web上登录，然后拿到cookie信息,vegeta命令执行时加入cookie即可。如：    


```
echo "GET http://github.com/" | ./vegeta attack -header="Cookie:xxx=QkJEMURGOEUzMkEwRkM1QUI1OUExQ0VFRjk2MDY3QjkwNTBDRjhDNjRCNkJBQzUwMDNEMEJCMDczNTg0MUM5RDU3RTRERUVDMEFBMUI0Rjc4QkE0RkFCQTg1MkNFMkZFMzIwN0I1QjFFQjlGRjk0MzkxOTRBRDE2RUJGRTAzQTQxRTg1NkU3RkQ4MEQ2MDhCNTI3RUM4QUExNTc2MDMxOQ%3D%3D; ddd=myersguo; PHPSESSID=xxx"  -duration=3s -rate=10 > results.bin`
```




### POST ###




post的参数都是放到文件中的,提前写到文件中:   


```
echo "POST http://localhost" |./vegeta attack -body /tmp/body.txt --duration=1s -rate=10 > results.bin

```


### 多个请求 ###



`vegeta`可以读文件来发起多个请求.   

请求文件为：target.txt，如下：   

```
POST http://goku:9090/things
@/path/to/newthing.json

PATCH http://goku:9090/thing/71988591
@/path/to/thing-71988591.json

POST http://goku:9090/things
X-Account-ID: 99
@/path/to/newthing.json

GET http://user:password@goku:9090/path/to
X-Account-ID: 8675309

DELETE http://goku:9090/path/to/remove
Confirmation-Token: 90215
Authorization: Token DEADBEEF

GET http://goku:9090/path/to/dragon?item=balls
HEAD http://goku:9090/path/to/success
```



### 实现原理 ###


### 新增逻辑 ###


`vegeta`的参数输入时从配置文件中读取的，而且参数是不可变的，我们这里简单修改一下它的源码，让它支持变量的动态生成。这里仅仅以生成随机数为例进行说明.    


(未完待续) 
