---
layout: wp
title: js对象clone
---

js 代码:

```js

a=5;
b=a;
b=4;
console.log(a,b);
```

结果输出：  

```
5 4
```

而：  

```js
a={}
b=a
b.title="title"
console.log(a,b);
```

>If you use an = statement to assign a value to a var with an object on the right side, javascript will not copy but reference the object


对象的赋值，是传递的引用，如果想要传值，则可以使用jquery:

```
$.extend({}, obj);

```

例子2： 

```
a = {
    'title': 'title',
    'child': {
        'info': {
            'title': 'child title',
        }
    }
}

b = $.extend({}, a.child);
b.info.title="new title";

console.log(a.child.info.title, b.info.title);

```


结果输出：new title new title


这是因为：  

>The merge performed by $.extend() is not recursive by default; if a property of the first object is itself an object or array, it will be completely overwritten by a property with the same key in the second or subsequent object. The values are not merged. This can be seen in the example below by examining the value of banana. However, by passing true for the first function argument, objects will be recursively merged.


第一个参数传true,进行 deepcopy,即对递归copy,对象的值也被copy了。   




