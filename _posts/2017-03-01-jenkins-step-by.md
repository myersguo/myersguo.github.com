---
layout: wp
title: jenkins enviroment step by step
---

## jenkins monkey plugin ##

在[android性能](https://myersguo.github.io/2017/03/02/android-performance.html)的文章中，我们说过了性能统计的方式。这里简单说一下怎么把性能统计用jenkins每天跑出来....   


###  准备工作 ###

* monkey + handphone   
* [jenkins](https://jenkins.io/)   
* [jenkins html public plugin](https://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin)   



### jenkin job ###

第一步：  

在源码管理中拉取源码**https://github.com/myersguo/PythonExamples.git/**   

![jenkins monkey job](/public/images/jenkins_monkey_job.png)   


在构建中添加shell/bat:   

```
cd adb
python tools/monkey.py  -p com.example.android.testing.uiautomator.BasicSample --pct-syskeys 0 --pct-motion 0 --throttle 300 --bugreport 1000

```

![jenkins monkey shell](/public/images/jenkins_monkey_shell.png)   



构建后，public html report:    


![jenkins public html](/public/images/jenkins_monkey_html.png)   


执行构建后，查看html report:   

构建之后，`jenkin html report`这个插件会把指定的`archive`的所有html(这里是workspace/$jobname/adb/output)复制到`jobs/$jobname/htmlreports`文件夹下。     

![jenkins public result](/public/images/jenkins_monkey_result.png)     


备注：如果构建中遇到了报错:    

```
Blocked script execution in 'index.html' because the document's frame is sandboxed and the 'allow-scripts' permission is not set.
```

需要把jenkins的[安全限制](https://wiki.jenkins-ci.org/display/JENKINS/Configuring+Content+Security+Policy)放开(jenkins 启动文件config.xml中配置-Dhudson.model.DirectoryBrowserSupport.CSP=)    


## jenkins uiautomator plugin  ##

(未完待续)   




### 参考资料 ###

[https://wiki.jenkins-ci.org/display/JENKINS/Home](https://wiki.jenkins-ci.org/display/JENKINS/Home)   

