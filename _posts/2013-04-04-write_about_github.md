---
layout: wp
title: 安装github，搭建个人博客
comments: true
---
#安装github and 搭建个人博客   


早起，研究SVN与git的区别：   
一个是分布式的，一个是集中式的：
GIT可以在本地建立版本库；SVN所有的版本控制都在服务器端。这样就造成SVN服务器的压力大、风险高。   
正由于这点，git对文件的diff控制更加的好，commit和push分开，同步更快。   

在我的老机器安装了[github for windows](http://windows.github.com/),开始在github上搭建一个个人博客。  

1\. 前言。  
github向每一个人免费提供[1G空间，无限流量](https://help.github.com/articles/what-is-my-disk-quota)。github提供个人[主页服务](http://pages.github.com/)。   

2\. 创建一个项目，项目名称必须为：用户名.github.com。
这样的项目名称表示这是你的个人主页，可以使用二级域名直接访问。如我的:[http://myersguo.github.com](http://myersguo.github.com "myersguo.github.com")   

3\. OK，创建好项目之后，开始建博客。   

3\.1 **tips:**   
github的个人主页是由jekyll生成的。[jekyll是一个由ruby驱动的静态页面生成工具](https://github.com/mojombo/jekyll#readme)。jekyll使用[YAML](https://github.com/mojombo/jekyll/wiki/yaml-front-matter)和[Liquid模板](http://liquidmarkup.org/)扩展。

> Diving In   
> Migrate from your previous system   
> Learn how the **YAML** Front Matter works   
> Put information on your site with Template Data   
> Customize the **Permalinks** your posts are generated with   
> Use the built-in **Liquid Extensions** to make your life easier   
> Use custom Plugins to generate content specific to your site

3\.2 了解了jekyll的点滴知识，我们就可以开始搭建个人博客了。  
如果对如何使用模板不是很清楚。先写一个hello,world。
> index.html  
> hello,world

3\.3 开始搭建个人博客：  
我的个人博客，直接套用的github上的CSS模板，自己只添加了index.html 和 _post文件等。    
分页参考的是[jekyll的分页模板](https://github.com/mojombo/jekyll/wiki/Pagination)
> 目录tips:   
> 以下划线开头的目录需被jekyll服务器解析后生成静态页   
> _layout: 放置页面模板  
> _posts: 放置静态文章页面   


3\.4 我的页面代码：   
[\_layout/wp.html](https://github.com/myersguo/myersguo.github.com/blob/master/_layouts/wp.html)  
[index.html](https://github.com/myersguo/myersguo.github.com/blob/master/index.html)   
[config](https://github.com/myersguo/myersguo.github.com/blob/master/_config.yml)   
更多参见[https://github.com/myersguo/myersguo.github.com/](https://github.com/myersguo/myersguo.github.com/)  


3\.5 本地调试  
本地调试需要安装ruby和jekyll。[安装步骤](http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html)  
> 1 下载ruby，安装C:\ruby  [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/)  
> 2 下载Ruby development kit[https://github.com/oneclick/rubyinstaller/downloads/](https://github.com/oneclick/rubyinstaller/downloads/)，解压。  
> 进入ruby dev kit目录：   
> ruby dk.rb init    
> ruby dk.rb install   
> 下载安装：gem install jekyll   
> 启动：jekyll --server --auto


如果出现一些编码问题：
>  Liquid Exception: incompatible character encodings: UTF-8 and GBK  

可能的解决方法是：更改控制台编码：chcp 65001   
或者对rb文件：  
rb 文件开头加上： 
\# encoding: UTF-8 
   

3\.6 如果觉得，学习jekyll很麻烦，使用[jekyllbootstrap](http://jekyllbootstrap.com/)或[octopress](http://octopress.org/)来快速地建立一个可更换主题模板的博客是个不错的选择。  


