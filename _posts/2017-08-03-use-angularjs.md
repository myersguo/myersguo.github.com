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

`apply` 的逻辑是使之进入`$digest` 循环：  

```
$apply: function(expr) {
  try {
    beginPhase('$apply');
    try {
      return this.$eval(expr);
    } finally {
      clearPhase();
    }
  } catch (e) {
    $exceptionHandler(e);
  } finally {
    try {
      $rootScope.$digest();
    } catch (e) {
      $exceptionHandler(e);
      // eslint-disable-next-line no-unsafe-finally
      throw e;
    }
  }
}
```

`$digest` 循环检查`$watch` 列表，判单是否`dirty`，逻辑如下：  

```
$digest: function() {
  do { // "while dirty" loop
    dirty = false;
    current = target;
    asyncQueue.length = 0;

    traverseScopesLoop:
    do { // "traverse the scopes" loop
      if ((watchers = current.$$watchers)) {
        // process our watches
        watchers.$$digestWatchIndex = watchers.length;
        while (watchers.$$digestWatchIndex--) {
          try {
            watch = watchers[watchers.$$digestWatchIndex];
            // Most common watches are on primitives, in which case we can short
            // circuit it with === operator, only when === fails do we use .equals
            if (watch) {
              get = watch.get;
              if ((value = get(current)) !== (last = watch.last) &&
                  !(watch.eq
                      ? equals(value, last)
                      : (isNumberNaN(value) && isNumberNaN(last)))) {
                dirty = true;
                lastDirtyWatch = watch;
                watch.last = watch.eq ? copy(value, null) : value;
                fn = watch.fn;
                fn(value, ((last === initWatchVal) ? value : last), current);
              } else if (watch === lastDirtyWatch) {
                // If the most recently dirty watcher is now clean, short circuit since the remaining watchers
                // have already been tested.
                dirty = false;
                break traverseScopesLoop;
              }
            }
          } catch (e) {
            $exceptionHandler(e);
          }
        }
      }
  } while (dirty || asyncQueue.length);
  
```

ok, 那 `$watch`队列是什么时候添加的呢？在watch的时候啊。   

```
 $watch: function(watchExp, listener, objectEquality, prettyPrintExpression) {
        var get = $parse(watchExp);

        if (get.$$watchDelegate) {
          return get.$$watchDelegate(this, listener, objectEquality, get, watchExp);
        }
        var scope = this,
            array = scope.$$watchers,
            watcher = {
              fn: listener,
              last: initWatchVal,
              get: get,
              exp: prettyPrintExpression || watchExp,
              eq: !!objectEquality
            };

        lastDirtyWatch = null;

        if (!isFunction(listener)) {
          watcher.fn = noop;
        }

        if (!array) {
          array = scope.$$watchers = [];
          array.$$digestWatchIndex = -1;
        }
        array.unshift(watcher);
        array.$$digestWatchIndex++;
        incrementWatchersCount(this, 1);

        return function deregisterWatch() {
          var index = arrayRemove(array, watcher);
          if (index >= 0) {
            incrementWatchersCount(scope, -1);
            if (index < array.$$digestWatchIndex) {
              array.$$digestWatchIndex--;
            }
          }
          lastDirtyWatch = null;
        };
      },

```

因此，我们可以梳理出来以下数据流：  

template -> direct(watch) -> compline(link) -> apply -> digest   



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

