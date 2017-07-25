---
layout: wp
title: 接口管理
comments: true
---

做接口管理的方案，目前有一下几种：   

* [api doc](http://apidocjs.com/)   
* [swagger](https://swagger.io/)  
* [api blueprint](https://apiblueprint.org/)   
* [sphinx-doc](http://www.sphinx-doc.org/en/stable/index.html)  
* [gitbook](https://github.com/GitbookIO/theme-api#readme)  

其中， api doc 是将 api 定义用注释的方式生成。其他都是文档管理的方式。是生成接口文档的一种工具。   

又没有更好的思路呢？    

目前，我能想到的思路是：    

类似 api doc，在代码中添加注释的方式，定义接口文档，然后调用 generator 扫描项目目录，上报接口到管理平台，管理平台负责接口定义和文档管理。   
即， 脚本上报接口定义, 平台负责前端展示。    


### 参考资料 ###   

[https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/](https://www.ibm.com/developerworks/cn/opensource/os-sphinx-documentation/)
