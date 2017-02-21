---
layout: wp
title: 简单的jenkins插件 
comments: true
---  

### 前言  ###

一般公司内部的发布系统和jenkins的持续集成结合时，往往官方的插件不足以满足我们的需求。因此我们需要自己动手，写一个定制化的jenkins插件。  

### 起步 ###  


在动手之前，一定要先走以下几步：   

*  搜索[plugin list](https://wiki.jenkins-ci.org/display/JENKINS/Plugins)接近你的需求的插件；  
*  搜索插件仓库https://github.com/jenkinsci   
*  google search 是否有类似需求讨论  
*  搜索邮件列表https://groups.google.com/forum/#!forum/jenkinsci-users   

当你发现所有人的需求都和你不同时，你就可以开始动手(copy and write)了。   


### 写一个简单的jenkins插件 ###

(前提是准备好eclipse,mvn)   

#### 添加jenkins的mvn仓库 ####

mvn的安装目录下的conf/settings.xml添加如下[配置](https://wiki.jenkins-ci.org/display/JENKINS/Plugin+tutorial#Plugintutorial-SettingUpEnvironment), 比如我的机器的配置为：    

```
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0" 
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
 
 <!--<settings> -->
  <pluginGroups>
    <pluginGroup>org.jenkins-ci.tools</pluginGroup>
  </pluginGroups>

  <profiles>
    <!-- Give access to Jenkins plugins -->
    <profile>
      <id>jenkins</id>
      <activation>
        <activeByDefault>true</activeByDefault> <!-- change this to false, if you don't like to have it on per default -->
      </activation>
      <repositories>
        <repository>
          <id>repo.jenkins-ci.org</id>
          <url>https://repo.jenkins-ci.org/public/</url>
        </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>repo.jenkins-ci.org</id>
          <url>https://repo.jenkins-ci.org/public/</url>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
  <mirrors>
    <mirror>
      <id>repo.jenkins-ci.org</id>
      <url>https://repo.jenkins-ci.org/public/</url>
      <mirrorOf>m.g.o-public</mirrorOf>
    </mirror>
  </mirrors>
</settings>
```


```
mvn -U org.jenkins-ci.tools:maven-hpi-plugin:create   
```

![jenkins](/public/images/jenkins.hello.png)    

然后，你就看到jenkins的插件Downloading....then一步一步的填写相关配置,你已经创建好了jenkins插件的helloworld工程。    


```
└─hello
    └─src
        └─main
            ├─java
            │  └─io
            │      └─github
            │          └─myersguo
            │              └─jenkins
            │                  └─hello
            └─resources
                └─io
                    └─github
                        └─myersguo
                            └─jenkins
                                └─hello
                                    └─HelloWorldBuilder
```

编译打包:`mvn install`    
将在target目录下生成hello.hpi(jenkins插件,hello.jar的封装包)     

打开jenkins进行安装，然后建一个job，执行以下就能看到熟悉的hello,world了。   

![jenkins plugin](/public/images/plugin.png)   
![jenkins plugin](/public/images/plugin2.png)     


### 写一个自己的插件 ###

上面利用[maven-hpi-plugin](https://github.com/jenkinsci/maven-hpi-plugin)自动创建了一个jenkins插件工程。我们写自己的插件就从上面的工程**按照约定**编写相应的工程即可。约定是什么呢？  

* resources: jelly/groovy 视图  
* main: 插件源码逻辑   

**插件视图设置**   

**config.jelly**配置插件的变量参数；   
**global.jelly**配置插件的全局参数（系统设置参数）;  

**插件代码逻辑**   

详情了解jenkins的架构可以阅读[官方文档](https://wiki.jenkins-ci.org/display/JENKINS/Architecture):jenkins基于stapler构建了基于url的MVC框架。我们只需要按照jenkins插件规范来写插件即可，这里修改默认生成的构建类，进行设置：  

构建的插件都要继承自`Builder`类，我们以创建文件为例写一个插件：  

通过注解DataBoundConstructor和[DataBoundSetter](https://github.com/stapler/stapler/blob/master/core/src/main/java/org/kohsuke/stapler/DataBoundSetter.java)将config.jelly的配置传递到插件对象中。    

[代码示例](https://github.com/myersguo/fileoperate_jenkins/blob/master/src/main/java/com/xiaomi/test/xmtest/fileoperate/ConfigUpdate.java)  

```
public class ConfigUpdate extends Builder {

    public String filePath = "";
    public String fileContent = "";
    public String fileOption = "overWrite";
    //config.jelly中的配置是数据来源
    @DataBoundConstructor
    public  ConfigUpdate(String filePath, String fileContent, String fileOption) {
        this.filePath = filePath;
        this.fileContent = fileContent;
        this.fileOption = fileOption;
    }
    //settter & getter
    @DataBoundSetter
    public void setFilePath(String outputFile) {
        this.filePath = outputFile;
    }
    @DataBoundSetter
    public void setFileContent(String fileContent){this.fileContent = fileContent;}

    @Override
    public boolean perform(AbstractBuild<?,?> build, Launcher launcher, BuildListener listener)
            throws InterruptedException,IOException {
        //插件业务逻辑,这里省略
        return true;
    }

    
    @Extension
    public static final class DescriptorImpl extends BuildStepDescriptor<Builder> {

        public DescriptorImpl() {
            load();
        }

        //构建检查
        public FormValidation doCheckFilePath(@QueryParameter String value)
                throws IOException, ServletException {
            if (value.length() == 0)
                return FormValidation.error("请设置文件路径");

            return FormValidation.ok();
        }

        public FormValidation doCheckFileContent(@QueryParameter String value)
                throws IOException, ServletException {
            if (value.length() == 0)
                return FormValidation.error("请输入文件内容");

            return FormValidation.ok();
        }

    }
}


``` 

这样，我们`copy and write`，jenkins的mvn插件给我把代码脚手架安装好。我们就按照步骤更改，就能写出定制化的插件了。   


(完)   


