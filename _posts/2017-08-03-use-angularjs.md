---
layout: wp
title: 使用 angularjs 中遇到的问题
---

angularjs 真是后端开发者的福音，一个看上去非常复杂的前端交互，用数据绑定的方式轻松实现，你不需要去关心DOM如何从A变成B的模样，你只需要去关心数据的形态，视图的改变交给 angularjs来实现即可。   

1. 
背景，我想要在路由改变的时候关闭modal对话框  
绑定了 routechangeStart，但是当 route change 的时候，没有反应。   

```
$rootScope.$on('$routeChangeStart', function(next, current) {
    console.log('hi');
}
```

但是通过使用 locationchangestart是可以实现的:   

```
$scope.$on('$locationChangeSuccess', function(event) {
	if($scope.modalInstance){
		$scope.modalInstance.dismiss('cancel');
	}
});
```

fixed:   

```
$rootScope.$on('$stateChangeStart', function(event, toState, toParams, fromState, fromParams){
	if($scope.modalInstance){
		$scope.modalInstance.dismiss('cancel');
	}
});
```

这里面有两点：   
1) 需要使用 $rootScope  
2) 需要使用 $stateChangeStart: 因为我用了 ui-router 代替了  ngRoute啊  


2.
redirect   

window.location.href  


3.
$scope.$apply   

<a class="jsbin-embed" href="http://jsbin.com/loraweduvi/1/embed?html,js,output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?4.1.1"></script>

看这个指令，为什么做了绑定，但是,没有生效呢？  

当我们的模版加载完毕时，也就是在linking阶段（Angular分为compile阶段和linking阶段），Angular解释器会寻找每个directive，然后生成每个需要的$watch。  

angularjs的事件循环机制是：当浏览器接收到可以被angular context处理的事件(调用了 $apply)时，$digest循环就会触发。$digest将会遍历$watch队列,进行dirty-checking。

看一下`ng-click` 的事件处理,里面`forceAsyncEvents`属于异步事件，

```
var forceAsyncEvents = {
  'blur': true,
  'focus': true
};
forEach(
  'click dblclick mousedown mouseup mouseover mouseout mousemove mouseenter mouseleave keydown keyup keypress submit focus blur copy cut paste'.split(' '),
  function(eventName) {
    var directiveName = directiveNormalize('ng-' + eventName);
    ngEventDirectives[directiveName] = ['$parse', '$rootScope', function($parse, $rootScope) {
      return {
        restrict: 'A',
        compile: function($element, attr) {
          var fn = $parse(attr[directiveName], /* interceptorFn */ null, /* expensiveChecks */ true);
          return function ngEventHandler(scope, element) {
            element.on(eventName, function(event) {
              var callback = function() {
                fn(scope, {$event:event});
              };
              if (forceAsyncEvents[eventName] && $rootScope.$$phase) {
                scope.$evalAsync(callback);
              } else {
                scope.$apply(callback);
              }
            });
          };
        }
      };
    }];
  }
);
```
下面这个是`echart`点击legend时的事件处理。需要手动调用 $apply(jQuery没有调用$apply，事件没有进入angular context)。

```
$scope.chart_config = {
    theme: 'default',
    dataLoaded: false,
    event: [{
        legendselectchanged: function (params) {
            var clicked = params.name;
            var oldSelected = params.selected;
            for(var i in oldSelected) {
                if (i==clicked) {
                    oldSelected[i] = true;
                }else {
                    oldSelected[i] = false;
                }
            }
            $scope.$apply(function() {
                $scope.chart_option.legend.selected = oldSelected;
            });
        }
    }],
};
```


(未完待续)

