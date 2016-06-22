---
layout: wp
title: 简单的jenkins插件 
comments: true
---  

### 需求描述  
代码的发布和配置的发布是隔离的，在jenkins发布代码后希望配置也发布，或者可单独发布配置;

配置的发布流程：   
1）从配置平台拉取配置 http get 请求，需要经过认证
2) 将配置内容写入文件中

或者：
直接下载配置文件

请求的参数格式:
project_id
env
site

返回的格式:
file_name
file_path
file_content

### 需要的功能
1）HTTP请求
2）写文件操作


### jenkins提供的功能
1) SCM plugin 不满足功能

### 从头开发一个jenkins支持此功能



### jenkins 插件指南(翻译)


#### 前言   
jenkins的一个优势就是插件特性。现在jenkins插件库里的插件成百上千，几乎每周都有新的插件发布到jenkins平台中。因此，在开发插件之前，应该先从插件库中搜索，是否现有的插件满足你的需求。

如果你认为你的需求独特，需要编写一个插件，请参考：  
1. 搜索[plugin list](https://wiki.jenkins-ci.org/display/JENKINS/Plugins)接近你的需求的插件；
2. 搜索插件仓库https://github.com/jenkinsci
3. google search 是否有类似需求讨论
4. 搜索邮件列表https://groups.google.com/forum/#!forum/jenkinsci-users
5. 加入jenkinscli-dev 邮件列表。

jenkins 插件中心：  
jenkins 社区鼓励插件开发者上传插件。为了方便其他用户可以通过jenkins平台安装插件，插件的开发者需要：  
* 提供插件的id,不能包含jenkins和plugin。例如：enkins-snapchat-notification-plugin，应该更改为snapchat-notification。
插件ID需要仔细选择,已经选定不可更改。
* 提供插件的描述。
* 上传插件代码到github(https://github.com/jenkinsci)
* 提供open source 的协议(大多数使用MIT)
* 注册jenkins帐号，方便反馈问题


#### 我的步骤
1) 搜索plugin list，查找关键词file,找到file system scm这个插件与需求类似。
https://wiki.jenkins-ci.org/display/JENKINS/File+System+SCM
2）

参考资料:  
1) https://wiki.jenkins-ci.org/display/JENKINS/Before+starting+a+new+plugin
2） https://wiki.jenkins-ci.org/display/JENKINS/Plugin+tutorial
3) jenkins plugin source code: 
https://wiki.jenkins-ci.org/display/JENKINS/GitHub+Repositories
4) https://issues.jenkins-ci.org/secure/Dashboard.jspa



