---
layout: wp
title: 笔记,JAVA启动命令
---

java的命令行启动命令。它创建一个JAVA的运行时环境，加载待执行的类并调用该类`main`方法。


```
java [options] class [ argument ...]
java [options] -jar file.jar [ argument ...]
```

#### options ####

命令行参数。  

#### class #### 

待处理的类名。第一个非option的参数即要调用的类名。java会在以下[三个地方搜索该类](http://docs.oracle.com/javase/7/docs/technotes/tools/findingclasses.html)：-Xbootclasspath/p:path;java扩展包(lib/ext);用户自定义的classpath;    


#### file.jar ####

待处理的jar包  

#### argument ####

传递给main函数的参数。   

#### options ####   

-client,-server:选择java hotspot虚拟机引擎。  
-classpath classpath/-cp classpath: 指定类的查找库（目录，JAR包；ZIP包，用:隔开），这个设置扩展环境变量CLASSPATH的的设置。   
-Dproperty=value 设置一个系统变量。可以使用`System.getProperty(property)`来获取变量值。  
-Xmsn:设置内存初始大小，eg -Xms6m;   
-Xmxn:设置内存最大值；   
-Xssn:设置线程stack大小;     


