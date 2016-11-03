---
layout: wp
title: sass unicode字符编码问题
---

在使用我司定制的UI库的时候，经常出现fontawesome字符乱码的问题。我试图从nginx上下手[解决](https://myersguo.github.io/2016/09/18/woff2-load-error.html),当时认为是woff2字体nginx不认，加了个mime type:  

```
types {
    ...
    #webfont
    application/font-woff                woff;
    application/font-woff2               woff2;
}
```

一个月后，字体的问题偶尔会出现，今天，我终于不能忍受这个问题了。偶然间，我发现我司FE编译的css和官方不同。bootstrap和fontawesome的css文件中content，官方的是:"\f263",而我们的是不可见的字符。难道是编译转换的，我自己做个实验，在我的sass文件中添加： 

```
$font-text: "\f26e";

.test-font{
    before: $font-text;
}

```

编译之后，变成了:

```
.test-font{
    before: '这里是不可见的字符';
}
```

参见：[sassmeister.com](http://www.sassmeister.com/gist/f438e2d834b915c77d90806b960baf22)   

果然是这个问题！   

我开始熟练的使用google,
```

果然是这个问题！   

我开始熟练的,
```

果然是这个问题！   

我开始熟练的使用google,这个问题果然很多人都遇到了：   

[sass issues#1395](https://github.com/sass/sass/issues/1395)  
[sass issues#620](https://github.com/sass/sass/issues/620)  

有人为此写了个[gulp的插件](https://github.com/Perkovec/gulp-sass-unicode)解决这个问题.  

在我打开多个google提示的网页和我将sass版本从`3.4.16`升级到`3.4.22`之后问题仍在，我似乎开始绝望了。   

网上说的解决办法就是，要unicode[转义一下变量](https://github.com/sass/sass/issues/1395#issuecomment-57658736)，但要改写所有的变量，岂不是太麻烦？  


```
// @source - [@Stephn-R](https://github.com/sass/sass/issues/1395#issuecomment-57483844)
// @description converts 1 or more characters into a unicode 
// @markup {scss}
// unicode("e655"); // "\e655"
@function unicode($str){
 @return unquote("\"")+unquote(str-insert($str, "\\", 1))+unquote("\"")
}
```

虽然我试图从sass的ruby[源码](https://github.com/sass/sass/blob/master/lib/sass/scss/rx.rb)中找到答案，但：
到了最后，我仍然没有找到问题的最佳解法。   

这个问题，你遇到过吗？  


