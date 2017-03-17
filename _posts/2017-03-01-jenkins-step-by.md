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




`[spoon](http://square.github.io/spoon/)`解决了基于instrumentation的测试用力的测试报告的功能。只需要使用spoon来执行你的测试用例即可：    

`java -jar spoon-runner.jar --apk app-debug.apk --test-apk app-debug-androidTest.apk --output ./results`   

在results下面生成的报告：    

![spoon results](/public/images/spoon_reports.png)   


so,我们用uiautomator实现了测试用例之后，用jenkins集成就更简单了，只需要在jenkins上添加BAT/SHELL：   

```
java -jar E:\jenkins_tools\spoon-runner.jar --apk E:\TestExamples\AndroidTestExample\app\build\outputs\apk\app-debug.apk --test-apk E:\TestExamples\AndroidTestExample\app\build\outputs\apk\app-debug-androidTest.apk --output E:\jenkins_tools\results
```


然后，public html reports，把spoon结果路径填写上即可。     

问题   

1:这样每次都只能保存一次测试结果，如何保存多份?   

2:每次都执行固定的测试，如果动态的执行测试用例?    

回答：  

1. 最简单的用python遍历目录，然后生成一个archive的index.html   
2. 固定有什么不好?哈哈哈     






### 参考资料 ###

[https://wiki.jenkins-ci.org/display/JENKINS/Home](https://wiki.jenkins-ci.org/display/JENKINS/Home)   

