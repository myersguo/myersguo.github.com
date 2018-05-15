---
layout: wp
title: Jmeter 介绍
comments: true
---

### 前言 ###

在做压力测试前，先思考一下：   

* 预期平均用户数是多少？   
* 预期高峰用户数是多少?   



### 概念 ###

#### Test Plan (测试计划) ####

测试计划，即 Jmeter 的执行内容，完整的测试计划包括： Thread Groups(线程组), logic controllers(逻辑控制器), sample generating controllers(取样控制器), listeners(监听器), timers(定时器), assertions(断言), configuration elements(配置元素).

一个测试计划，至少要包含: 一个线程组，一个或多个取样器。  

#### Thread Groups(线程组) ####

测试计划的入口。用于设置, 线程数，递增线程时间、总的运行次数与时间(持续时间)。  

eg:   

```
Test Plan
  Thread Group
    - Once Only Controller
      * Login Request (an HTTP Request)
    - Load Search Page (HTTP Sampler)
    - Interleave Controller
      * Search "A" (HTTP Sampler)
      * Search "B" (HTTP Sampler)
      * HTTP default request (Configuration Element)
    - HTTP default request (Configuration Element)
    - Cookie Manager (Configuration Element)
```

上面的测试线程组执行一次 only once controller, 然后加载 search page, 交替执行 search a/b（按顺序交替选择），执行顺序是：   

* Configuration elements
* Pre-Processors
* Timers
* Sampler
* Post-Processors (unless SampleResult is null)
* Assertions (unless SampleResult is null)
* Listeners (unless SampleResult is null)


#### controllers( 控制器) ####

JMeter 有两种控制器：Samplers and Logical Controllersa 。
Samplers: 请求方式。（http, ftp, tcp etc）  
Logical controllers: 发送请求的逻辑，loop/while/once only etc.   


#### Listeners(监听器) ####

监听器用来对 JMeter test cases 的执行进行展示、汇总、分析。View Results Tree, Graph Results listeners,Aggregate Report 等等。   

#### Timers(定时器) ####

用于增加请求间的延时。   

#### assertions (断言) ####

用来测试、标记请求是否成功   

#### Pre-Processor/Post-Processor ####

用于请求的前后置处理。比如使用 HTTP URL Re-writing Modifier 来重置URL。   


### [分布式部署](https://jmeter.apache.org/usermanual/remote-test.html) ###

我们使用一台机器来进行说明（实际上，最好每台机器部署一个slave 节点）。     

启动 server, 端口号 9091/9092/9093:   

```
SERVER_PORT=9091  /usr/local/Cellar/jmeter/3.2/libexec/bin/jmeter-server -Djava.rmi.server.hostname=localhost
SERVER_PORT=9092 /usr/local/Cellar/jmeter/3.2/libexec/bin/jmeter-server -Djava.rmi.server.hostname=localhost
SERVER_PORT=9093 /usr/local/Cellar/jmeter/3.2/libexec/bin/jmeter-server -Djava.rmi.server.hostname=localhost
```

使用 GUI 方式，启动 client or master,    

vi /usr/local/Cellar/jmeter/3.2/libexec/bin/jmeter.properties, 修改 remote_hosts:    

```
remote_hosts=127.0.0.1:9091,127.0.0.1:9092,127.0.0.1:9093
```

使用非 GUI 方式启动：   

```
jmeter -n -t  ~/Downloads/URLRewritingExample.jmx -R127.0.0.1:9093,127.0.0.1:9091,127.0.0.1:9092
```

### 监控 ###

这里在 mac 下安装 influxdb 与 grafana 来配置[实时监控](https://jmeter.apache.org/usermanual/realtime-results.html)  
```
brew install influxdb
brew services start influxdb
```

influx -precision rfc3339  

```
CREATE DATABASE grafana
CREATE DATABASE jmeter
SHOW DATABASES;
```


```
brew install grafana
brew services start grafana
```

打开： http://localhost:3000/ 登录设置   
添加 infuxdb 源。   

jmeter 测试时，添加 backend listeners:`http://127.0.0.1:8086/write?db=jmeter` 试试查看数据。可以在命令行中查看  influxdb 中的数据：   

```
SHOW MEASUREMENTS;
SHOW TAG KEYS FROM "jmeter";
select * from jmeter
```
维度：  


```
show field keys
time	application	avg	count	countError	endedT	hit	max	maxAT	meanAT	min	minAT	pct90.0	pct95.0	pct99.0	startedT	statut	transaction
```

### 最佳实践 ###

#### 定义变量 ####  

User Defined Variables, 定义变量：  
eg: name: resources_folder, value: /, desc: folder path   

Define CSV DATA:  
name: loginData   
Variable Name(comma-delimited): login, password   
[share mode](http://jmeter.apache.org/usermanual/component_reference.html#CSV_Data_Set_Config): All threads，所有线程组共享文件；Current thread group: 当前的线程组共享文件；Current thread: 线程共享，线程组内不共享。    

假如你有1万个用户的文件，想要每个线程的用户不同，则设置成Current thread group；如果可以相同，则可以设置成 Current thread.    

    

#### 减少资源使用  ####

* 使用非 GUI 模式： jmeter -n -t test.jmx -l test.jtl  
* 尽量少使用 Listeners, 不在压测是使用： "View Results Tree" or "View Results in Table"   
* 不实用 functional mode  
* Use CSV output rather than XML   
* 只保存必须的数据   
* 尽量少使用断言   
* 



### 提醒 ###

`Using GUI mode as described here should only be used when debugging your Test Plan. To run the real load test, use NON-GUI mode.`  

压测千万别用 GUI 模式，一定要用 模式.  



### 参考资料 ###

[使用 JMeter 完成常用的压力测试](https://www.ibm.com/developerworks/cn/opensource/os-pressiontest/)   
[How to Use Grafana to Monitor JMeter Non-GUI Results - Part 2](https://www.blazemeter.com/blog/how-to-use-grafana-to-monitor-jmeter-non-gui-results-part-2)   

