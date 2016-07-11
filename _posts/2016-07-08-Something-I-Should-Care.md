---
layout: wp
title: 接口返回的定义  
---

现在的开发流程，不论是否是teamwork,还是个人单打独斗，都会遇到接口的定义问题。比如：前后端分离，后端返回给前端数据应该怎么定义，方便前端页面使用。如何定义健壮的接口返回。   

我参考遇到的项目，总结如下：   

1. 一级字段自己定义。即，先定义好要返回的结构，然后在此基础上填充数据.eg:  
```
{
total_count: 0,
current_page: 1,
page_size: 10,
all_items: {
},
error:0,
add_time:0,
}
````  

2. 



参考资料：  
1. http://www.restapitutorial.com/lessons/restquicktips.html  
2. https://phraseapp.com/blog/posts/best-practice-10-design-tips-for-apis 3. 
