---
layout: wp
title: Jenkins 相关 
---

1. 好用的jenkins 插件  

* [test-results-analyzer](https://wiki.jenkins-ci.org/display/JENKINS/Test+Results+Analyzer+Plugin)  
测试结果分析，可以直观的展示测试用例的执行情况，无需进入每一个测试报告中查看。   

* Project Health Report    
项目构建状态统计工具   

* CVS Plugin   
统一登录认证服务接入插件。   

* xunit   











2. Jenkins任务的执行   
如何做到每日自动执行：使用构建触发器:
Pull SCM: 检测是否有更新，进行触发；  
periodically： 计划任务；  
Build after other projects are built: 关联触发；   
trigger by remote: 远程调用；   

>>
    使用git hooks,在项目下.git\hooks文件夹下游很多hook的例子    
    在post-update.sh中post请求到jenkins:http://JENKINS地址/job/JOB_NAME/build?token=TOKEN   

    如果使用gitlab可以使用相关插件   
    Use the following URL to trigger build remotely: JENKINS_URL/job/Demo/build?token=TOKEN_NAME or /buildWithParameters?token=TOKEN_NAME  
    Optionally append &cause=Cause+Text to provide text that will be included in the recorded build cause.


3. 










