---
layout: wp
title: Spring mvc Hello World
---

最近，可能要接触很多 JAVA 的项目，学习一下 SSM 框架，没有坏处，走起。  


### step by step ###

1. 添加一个maven项目，

`mvn -B archetype:generate  -DgroupId=myersguo.github.io -DartifactId=myweb`   

编辑 pom.xml 文件，添加 spring mvc 依赖：  

```
 <!--
    定义变量
    -->
    <properties>
        <spring.version>3.2.3.RELEASE</spring.version>
    </properties>
    <!--
    添加依赖
    -->
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
    </dependencies>
```

mvn install

2. 新建文件 webapp\WEB-INF\web.xml:    
   新增文件 resources\context\context-web.xml   

mkdir src\main\webapp\WEB-INF

vi web.xml:   

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app
         xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         metadata-complete="false" version="3.0"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">

    <servlet>
        <servlet-name>webapp</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/webapp.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>webapp</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!-- 
        默认加载[servlet-name]-servlet.xml ,这里使用自定义配置
    -->
    <context-param>
        <param-name>contextConfigLocation>
        <param-value>classpath*:context/context-*.xml</param-value>
    </context-param>
    <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>
</web-app>
```

mkdir src\main\resources\context,vi context-web.xml:  


3. 新建一个 controller和 view:   

``
package myersguo.github.io.controllers;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Controller
public class IndexController {
    //@RequestMapping("/")
    @RequestMapping(value = "/", method = RequestMethod.GET)
    public String index(ModelMap model/*, HttpServletRequest request,
                        HttpServletResponse response*/){
        model.addAttribute("message", "myersguo");
        return "index/index";
    }
}
```

views:   

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="X-UA-Compatible" content="IE=Edge" >
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=yes">
    <head>
        <title>title</title>
        <link rel="stylesheet" href="/css/bootstrap.min.css" />
    </head>
<body>
<h2>${message}</h2>
<script src="/js/lib/bootstrap.min.js"></script>
</body>
</html>
```

执行 mvn jetty:run 之后访问： http://localhost:8001/   

看到：  myersguo

详细例子，参考： [https://github.com/myersguo/TestExamples/tree/master/java/web](https://github.com/myersguo/TestExamples/tree/master/java/web)    

大功告成！  

我们从其他角度看一下 spring


### 启动 ###

在 webapp\WEB-INF\web.xml 中配置 servlet：   


```
<web-app>
    <servlet>
        <servlet-name>example</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

</web-app>
```

容器启动后，加载 spring servlet: DispatcherServlet, 交由它来处理。   

web-app的 listener在启动时初始化，spring 加载ContextLoader,然后 initWebApplicationContext,ContextLoader加载xml中定义的资源、组件。   

### 控制器注解 ###

在 ` xml` 中配置组件扫描路径，这样spring就能自动检测(ClassPathScanningCndidateComponetProvider)  

`
    <context:component-scan base-package="org.springframework.samples.petclinic.web"/>
`


### 资源汇总 ###

快速开始一个项目：   [start.spring.io](http://start.spring.io/) & [Sprint boot](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-installing-the-cli)    
tomcat 如何调用servlet: classloader [https://www.ibm.com/developerworks/cn/java/j-lo-classloader/](https://www.ibm.com/developerworks/cn/java/j-lo-classloader/) && [https://www.ibm.com/developerworks/cn/java/j-lo-servlet/)[https://www.ibm.com/developerworks/cn/java/j-lo-servlet/)   
