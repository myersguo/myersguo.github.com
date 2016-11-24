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

```
If you use an = statement to assign a value to a var with an object on the right side, javascript will not copy but reference the object
```

对象的赋值，是传递的引用，如果想要传值，则可以使用jquery:

```
$.extend({}, obj);

```


(done)


