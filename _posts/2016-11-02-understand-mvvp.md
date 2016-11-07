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
有没有更好的办法?   

### MVVP 前端框架 ###    

[AngularJS](https://en.wikipedia.org/wiki/AngularJS)在2010年发布,首先提出了解决方案：   

AngulaerJS发明了指令，重新定义了模版。   

### 实现 ###   

使用vue.js来实现上面的[需求](https://jsfiddle.net/myersguo/okrefvjt/)：  

```
<script src="https://cn.vuejs.org/js/vue.js"></script>
<div id="app">
  <select  id="app" v-model="message">
  <option value="1">1</option>
  <option value="2">2</option>
  <option value="3">3</option>
  </select>
  you select option {{ message }}
</div>

<script>
var app =new Vue({
  el: '#app',
  data: {
    message: 1
  }
})
</script>
```


<iframe style="width:100%" src="//fiddle.jshell.net/myersguo/okrefvjt/show/light/" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>


简直帅的不敢相信，有什么理由不学习、使用一下这种先进生产力?  



### 扩散思考 ###   

其实你不需要重新发明一种语言，你只需要重新定义你自己的指令。你能够parse你的指令，让它生成已知的文档就OK。感觉这和很多自动化测试框架类似，用现有的语言构建『特定的语法』(就像是Domain-specific language,[DSL](https://en.wikipedia.org/wiki/Domain-specific_language))，然后对其进行解析。   




### 参考资料 ###   

[mvv wiki](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel)   
[http://www.tych.io/tech/2014/07/21/template-of-angularjs.html](http://www.tych.io/tech/2014/07/21/template-of-angularjs.html)  
[http://blog.qivhou.com/translation/2014/04/03/Understanding-MVVM-A-Guide-For-JavaScript-Developers/](http://blog.qivhou.com/translation/2014/04/03/Understanding-MVVM-A-Guide-For-JavaScript-Developers/)  
[Single-page application,SPA](https://en.wikipedia.org/wiki/Single-page_application)  
[Javascript Template library](https://en.wikipedia.org/wiki/JavaScript_templating)  


