---
layout: wp
title: 算法之殇
---

最近公司的面试我有机会参与了几次，在一个对算法要求较高的公司面试，必然要出几个算法题，比如二叉树、快排等等。然后，作为面试官的我，面对算法却是很是头疼的，长久没有接触算法，已经对算法有点抵触了。今天正好有空，温习了下大一数据结构里面学习的排序算法，当写到快排时，我的内心是崩溃的。    
<div style="height=500px;">
    <script async src="//jsfiddle.net/8teynqdj/embed/result/"></script>
</div>
<a class="jsbin-embed" href="//jsbin.com/vibukis/embed?html,output">edit</a>


```
<!DOCTYPE html>
<html ng-app="test"><head><meta charset="utf-8"><title>sort</title>
</head><script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.0/angular.min.js"></script>
<script>
  var app = angular.module("test", []);
  function SortCtrl($scope, $timeout){
    $scope.init = function(){
      $scope.arr = [7,3, 61, 5, 45, 7, 2, 4, 9, 24];
      $scope.sort_done = false;
      $scope.counter = {'compare':0, 'swap':0};
    }
    $scope.init();
    function swap(arr, i, j){
       var temp = arr[i];
       arr[i] = arr[j];
       arr[j] = temp;
       $scope.counter.swap++;
    }
    $scope.bubblesort = function() {
      var len = $scope.arr.length;
        for(var i=0; i<len; i++){
          for(var j=1;j<len-i; j++) {
            $scope.counter.compare++; 
            if($scope.arr[j] < $scope.arr[j-1]){
              swap($scope.arr, j, j-1);
            }
          }
        }
    }
    //compare, then swap
    $scope.insertSort = function(){
      var len = $scope.arr.length;
      for(var i=1; i<len; i++){
        var _tmp = $scope.arr[i];
        $scope.counter.compare++;
        for(var j=i;j>0 && $scope.arr[j-1] > _tmp; j--){
          $scope.arr[j] = $scope.arr[j-1];
          $scope.counter.swap++;
        }
        $scope.arr[j] = _tmp;
      }
    }
    //select then swap
    $scope.selectSort = function () {
      var len = $scope.arr.length;
      for(var i=0; i<len; i++){
        var min = i;
        for(var j=i+1; j<len; j++){
          $scope.counter.compare++;
          if($scope.arr[j] < $scope.arr[min]) {
            min = j;
          }
        }
        //swap min, i
        swap($scope.arr, i,min);
      }
    }
    function partition(arr, pivot, left, right){
       var pivotValue = arr[pivot],
           partitionIndex = left;

       for(var i = left; i < right; i++){
        $scope.counter.compare++;
        if(arr[i] < pivotValue){
          swap(arr, i, partitionIndex);
          partitionIndex++;
        }
      }
      swap(arr, right, partitionIndex);
      return partitionIndex;
    }
     
    //quick sort
    //https://khan4019.github.io/front-end-Interview-Questions/sort.html#quickSort
    function quickSort(arr, left, right){
       var len = arr.length, 
       pivot,
       partitionIndex;

      if(left < right){
        pivot = right;
        partitionIndex = partition(arr, pivot, left, right);

        quickSort(arr, left, partitionIndex - 1);
        quickSort(arr, partitionIndex + 1, right);
      }
      return arr;
    }
    
    $scope.doQuickSort = function() {
      quickSort($scope.arr,0, $scope.arr.length);
    }
  }
  app.controller('SortCtrl', SortCtrl);
</script>
<body>
<div ng-controller="SortCtrl">
    <style>
        .data-item {
            float: left;
            width: 20px;
            background-color: #c0c0c0;
            border: 1px solid #080808;
        }
    </style>
    <button ng-click="init();bubblesort();">BubbleSort</button>
    <button ng-click="init();insertSort();">InsertSort</button>
    <button ng-click="init();selectSort();">SelectSort</button>
    <button ng-click="init();doQuickSort();">QuckSort</button>
  
  
    <button ng-click="init()">init</button>
    <button>commpare:{{counter.compare}},swap{{counter.swap}}</button>
    <div>
        <div ng-repeat="item in arr track by $index" class="data-item" 
        ng-style="{'height': item*5+'px'}">{{item}}</div>
    </div>
</div>
  </body></html>
```

怎么办？刷刷题，提升点自信心吗？   


