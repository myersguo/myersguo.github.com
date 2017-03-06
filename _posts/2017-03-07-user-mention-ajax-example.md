---
layout: wp
title: mention.js ajax example
---

![mention.js](/public/images/mention.png)  


`Mention.js`是对输入用户名时进行提示的方法。[github](https://github.com/jakiestfu/Mention.js/)上提供的例子中没有`ajax`的，这里提供一下示例。      

```
 $('[data-user-mention]').mention({
     queryBy: ['name', 'user_name'],
     typeaheadOpts: {
         source: function(query, process) {
             //get last mention name
             var caratPos = this.$element[0].selectionStart;
             for (var i = caratPos; i >= 0; i--) {
                 if (query[i] == '@' || /\W/.test(query[i])) {
                     break;
                 }
             }
             var replace = query.substring(i, caratPos);
             if (replace.substring(0, 1) != '@') return;
             return $.ajax({
                 'type': 'GET',
                 'url': '/users/suggest',
                 'data': 'keyword=' + replace.substring(1),
                 dataType: 'json',
                 success: function(json) {
                     result = json; //deal with your special condition
                     process(result);
                 }
             });
         }
     }
 });
```

说明一下：    
对`[data-user-mention]`的元素添加mention事件。对输入进行处理。例如输入为：   

`@guoliangyong 和 @myers`     

那么，第二次输入@myers的时候，应该取的query是`@myers`，因此要获取最后一次的mention。    

这里的ajax我是以我的项目为例示例，需要修改成你自己的ajax请求。     


