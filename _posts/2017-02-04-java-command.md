---
layout: wp
title: write java first
---

### java command line ###

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


#### 其他 ####

启动java 程序：   

```
#!/bin/bash
cd `dirname $0`
BIN_PATH=`pwd`
cd ..
SRC_PATH=`pwd`
JAVA_HOME="/home/work/app/java"
LIB_DIR=$SRC_PATH/lib/*.jar
JAVA_OPTIONS=“”
JAVA_CLASS=""
$JAVA_HOME/bin/java $JAVA_OPTIONS -cp $LIB_DIR $JAVA_CLASS
```

启动后可以通过jps查看进程(jps -lvV)   

### gradle ###

gradle 默认会在当前目录下寻找build.gradle并根据settings.gradle寻找子项目.

`settings.gradle`包含所有的子模块。   

build.gradle常见属性：   


allprojects: project及所有子project的属性。
dependencies: 依赖管理。 
buildScript:构建脚本依赖   
buildDri: 生成脚本的目录  
rootDir: 项目根目录  
rootProject: 父项目project   


#### android compile ####

![images](/public/images/build-process_2x.png)   

android {
    compileSdkVersion 编译版本
    buildToolsVersion 构建版本    

    defaultConfig { //默认配置
    }
    buildTypes { //构建类型,debug or release

    }
    productFlavors { //产品分类，不同的渠道包
    
    }
}

### 参考资料 ###


