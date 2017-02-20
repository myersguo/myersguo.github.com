---
layout: wp
title: android version code & version name
---

android在AndroidManifest.xml中对APP进行版本的说明。其中:  

**versionCode用于版本更新的比较。**     



```
versionCode - 一个整数，用作内部版本号。此数字仅用于确定一个版本是否比另一个版本更新，数字越大表示版本越新。这不是显示给用户的版本号；后者通过下面的 versionName 设置设定。
```

**versionName用于APP的版本展示.**    

```
versionName - 一个字符串，用作显示给用户的版本号。可以作为原始字符串或对字符串资源的引用指定此设置。
此值是一个字符串，您可以使用 <major>.<minor>.<point> 字符串或任何其他类型的绝对或相对版本标识符来描述应用版本。除了向用户显示之外，versionName 没有其他用途。
```


一般情况下，为了区分，versionCode和versionName都是一一对应的。    
比如，版本号：`2.0.1`可以对应versionCode为：`20001`即replace`.`to`0`.   




[软件的版本号](https://en.wikipedia.org/wiki/Software_versioning)一般是对软件的版本信息的唯一标识。一般为：  

>major.minor[.build[.revision]]   
>or   
>major.minor[.maintenance[.build]]   



