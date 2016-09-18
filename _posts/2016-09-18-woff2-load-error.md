---
layout: wp
title: woff2加载失败
---
项目中使用fontawesome,但加载页面的时候经常出现woff2加载失败的问题。google了一下，怀疑是nginx不认这个扩展名，所以加载的时候出错。看一下nginx默认的MIME设置：   

```
http {
        include       mime.types;
        default_type  application/octet-stream;
```

默认设置为 application/octet-stream.   
如果类型存在，则设置为已经设置的类型，简单看下mime.types   

```
types {
    text/html                             html htm shtml;
    text/css                              css;
    text/xml                              xml;
    image/gif                             gif;
    image/jpeg                            jpeg jpg;
    application/x-javascript              js;
    application/atom+xml                  atom;
    application/rss+xml                   rss;

#....省略部分

```

添加队woff2的支持  

```
    #webfont
    application/font-woff                woff;
    application/font-woff2               woff2;
```

目前没有遇到问题，遇到了问题再修补文章。   




