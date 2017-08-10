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


2. redirect   

window.location.href  


(未完待续)

