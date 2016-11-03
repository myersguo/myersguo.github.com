---
layout: wp
title: understand mvvp
---

我有一个前端的需求，一个列表


```
<select>
<option value="1">ID1</option>
<option value="2">ID1</option>
<option value="2">ID1</option>
</select>
```
当我选择不同的模ID的时候，点击详情，需要显示该ID对应的详细信息。假设信息的数据是：  


```
[{id:1,name:'id1'}, {id:2,name:'id2'}, {id:3,name:'id3'}]
```

这个需求怎么做？js写一个`onchange`事件，当选择不同值时，渲染不同的数据内容。   






































参考资料：  
[http://blog.qivhou.com/translation/2014/04/03/Understanding-MVVM-A-Guide-For-JavaScript-Developers/](http://blog.qivhou.com/translation/2014/04/03/Understanding-MVVM-A-Guide-For-JavaScript-Developers/)  

