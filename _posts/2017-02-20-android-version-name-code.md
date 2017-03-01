---
layout: wp
title: android version code & version name
---

android在AndroidManifest.xml中对APP进行版本的说明。其中:  

**versionCode用于版本更新的比较。**     


>versionCode - 一个整数，用作内部版本号。此数字仅用于确定一个版本是否比另一个版本更新，数字越大表示版本越新。这不是显示给用户的版本号；后者通过下面的 versionName 设置设定。


**versionName用于APP的版本展示.**    

>versionName - 一个字符串，用作显示给用户的版本号。可以作为原始字符串或对字符串资源的引用指定此设置。
>此值是一个字符串，您可以使用 <major>.<minor>.<point> 字符串或任何其他类型的绝对或相对版本标识符来描述应用版本。除了向用户显示之外，versionName 没有其他用途。


一般情况下，为了区分，versionCode和versionName都是一一对应的。    
比如，版本号：`2.0.1`可以对应versionCode为：`20001`即replace`.`to`0`.   




[软件的版本号](https://en.wikipedia.org/wiki/Software_versioning)一般是对软件的版本信息的唯一标识。一般为：  

>major.minor[.build[.revision]]   
>or   
>major.minor[.maintenance[.build]]     


这个在`JAVA`版本中也有：   

```
major  minor Java platform version 
45       3           1.0
45       3           1.1
46       0           1.2
47       0           1.3
48       0           1.4
49       0           1.5
50       0           1.6
51       0           1.7
52       0           1.8
```

有时候我们看到：   

```
requires Java7 or later, but you are running 1.6.0_37-b06 
java.lang.UnsupportedClassVersionError: 50.0
```
这里面出现了三个版本相关的词：`JAVA7`,`1.6.0_37-b06`,`50.0`,乍一看，让人迷惑不解。    
类似这样的报错。50.0，就是对应的版本：java1.6,即JAVA 6,其中Version 1.6是给开发者看的(version string)。JAVA 6是给平台或产品名称(Platform/Product Name)       



