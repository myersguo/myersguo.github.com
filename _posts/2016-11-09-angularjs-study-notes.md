---
layout: wp
title: angularjs tutorial
---

### 概念 ###   

angular,$scope,modal,controller,view,service,filter,director,


### DI ###  

>Components such as services, directives, filters, and animations are defined by an injectable factory method or constructor function. These components can be injected with "service" and "value" components as dependencies.
>
>Controllers are defined by a constructor function, which can be injected with any of the "service" and "value" components as dependencies, but they can also be provided with special dependencies. 
>
>The run method accepts a function, which can be injected with "service", "value" and "constant" components as dependencies. Note that you cannot inject "providers" into run blocks.
>
>The config method accepts a function, which can be injected with "provider" and "constant" components as dependencies. Note that you cannot inject "service" or "value" components into configuration.

工厂方法：  

```
angular.module('myModule', [])
.factory('serviceId', ['depService', function(depService) {
  // ...
}])
.directive('directiveName', ['depService', function(depService) {
  // ...
}])
.filter('filterName', ['depService', function(depService) {
  // ...
}]);
```

#### 控制器 ####  

someModule.controller('MyController', ['$scope', 'dep1', 'dep2', function($scope, dep1, dep2) {
  ...
  $scope.aMethod = function() {
    ...
  }
  ...
}]);

#### 依赖注入 Dependency Annotation ####  

>Using the inline array annotation (preferred)
>Using the $inject property annotation
>Implicitly from the function parameter names (has caveats)
>

##### inline array annotation ##### 

```
someModule.controller('MyController', ['$scope', 'greeter', function($scope, greeter) {
  // ...
}]);
```

##### $inject property annotation ##### 

```
var MyController = function($scope, greeter) {
  // ...
}
MyController.$inject = ['$scope', 'greeter'];
someModule.controller('MyController', MyController);
```



#### MVC ####    

`Model`: 数据对象。它的变更将影响对应的view,同时更新view也将改变数据对象。       
`View & Template`: html的模版,modal数据的映射   
`Controller`:处理方法。     

![mvc images](https://docs.angularjs.org/img/tutorial/tutorial_02.png)   


#### module(name, requires, configFn) ####   

>模块中可以定义多个服务。模块包含：服务，指令，过滤器，配置信息等。ng默认提供一个ng的模块，它提供了$http,$q等服务。  



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

#### module定义 ####  

var module = angular.module('module_name', ['依赖']);

配置：  

module.config(['provider'], function(){}]);
or:  

var module = angular.module('module_name', [])
    .config(['provider', function(){}]);


#### provider ####  


#### directives ####   

指令。


#### service ####   

`Angular services are substitutable objects that are wired together using dependency injection (DI). You can use services to organize and share code across your app.`   

服务是对一些对象的集合。   

```
module.factory('serviceId', ['depService', function(depService) {
}]);

or
module.service('serviceId', ['depService',function(depService){}]);
```



### 内置服务 ### 

$route,$http,$q


#### 参考资料 ####  

* [http://www.ibm.com/developerworks/cn/web/1406_rentt_angularjs/index.html](http://www.ibm.com/developerworks/cn/web/1406_rentt_angularjs/index.html)   
* [https://docs.angularjs.org/guide/introduction](https://docs.angularjs.org/guide/introduction)  

* [http://www.angularjs.cn/A0a6](http://www.angularjs.cn/A0a6)  
* [http://hellobug.github.io/blog/angularjs-directive-2-compile-vs-link/](http://hellobug.github.io/blog/angularjs-directive-2-compile-vs-link/)  



