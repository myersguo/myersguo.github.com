---
layout: wp
title: angularjs tutorial
---

### 概念 ###   

angular,$scope,modal,controller,view,service,filter,director,


#### MVC ####    

`Model`: 数据对象。它的变更将影响对应的view,同时更新view也将改变数据对象。       
`View & Template`: html的模版,modal数据的映射   
`Controller`:处理方法。     

![mvc images](https://docs.angularjs.org/img/tutorial/tutorial_02.png)   

   



#### module(name, requires, configFn) ####   

name: module的名称   
requires:   
configFn: 相当于module.config函数，对module配置化   


> A module is a collection of services, directives, controllers, filters, and configuration information.
> angular.module用来配置injector   


```js
创建module
var myModule = angular.module('myModule', []);
//注册服务
myMoDule.value('appName', 'myCoolApp');
//配置已存在的服务
myModule.config(['$locationProvider', function($locationProvider) {
    $locationProvider.hasPrefix('!');
}]);
```

***module相当于定义一个独立的app***,

#### directives ####   

指令。


#### service ####   





#### 参考资料 ####  

* [http://www.ibm.com/developerworks/cn/web/1406_rentt_angularjs/index.html](http://www.ibm.com/developerworks/cn/web/1406_rentt_angularjs/index.html)   

