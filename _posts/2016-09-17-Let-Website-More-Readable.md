---
layout: wp
title: 做好网页设计
---
之前写过一篇[写给大家看的设计书的读书笔记](http://myersguo.github.io/2013/04/06/note_about_nondesigners.html),发现3年过去了，在设计上并没有长进; 最近在设计前端页面的时候，经常绞尽脑汁，不知道如何设计布局，页面应该怎么呈现给用户，应该应用什么样的页面效果？怎么设计网页，才能让用户用起来自然流程呢？  

自己简单看了几个站点的页面设计，简单总结，方便以后参考：   

1）重点突出。   

* 字体大小要区分，不能通篇一种字体大小。突出显示可以使用h1,h2,h3这样的标题。      

* 颜色背景的区分。突出表现，可以使用鲜艳的背景色，比如，错误提示(error notification);  

* 使用图标。常见的网页图标，可辅助文字表述。  

2）布局自然。  

* 单列布局。使用内容少、结构单一的页面。即使如此，单列的宽度也不亦太宽。现在的页面少有单列的了，且看几个例子：
    medium的blog[内容页](https://blog.medium.com/a-look-at-recent-improvements-to-responses-568f78bd2327#.fkqu691ew),宽度:700px  
    facebook的登录页面的输入框[宽度](https://www.facebook.com/login.php?login_attempt=1&lwv=110)：612px  
    twitter的时间流(多列中的单列)[content-main](https://twitter.com/)宽度：590px  

* 两列布局：如果结构稍微复杂一些，比如medium的首页左侧显示文章列表，右侧显示其他功能列表：editor's picks,reading roulette,e-friends forever。文章列表宽度：660px, 另一列：332px.  

* 三列布局：页面结构非常复杂，需要多层展现的时候，就要用到3列布局。   
    jd.com首页：左侧导航(210px); slide(730px);右侧功能栏：233px; 楼层布局：固定推荐：330px,其他展示：880px.  

总结到此，简单看下多栏比例：   
    1:2
    1:3.5:1  

在CSS布局中，960grid说道：  

>All modern monitors support at least 1024 × 768 pixel resolution. 960 is divisible by 2, 3, 4, 5, 6, 8, 10, 12, 15, 16, 20, 24, 30, 32, 40, 48, 60, 64, 80, 96, 120, 160, 192, 240, 320 and 480. This makes it a highly flexible base number to work with.  

我简单把页面分成3列，然后只有一列重点突出，可能想的比较『粗糙』。。。。   

 
TODO....  




