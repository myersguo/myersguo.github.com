---
layout: wp
title: 关于搜索引擎
comments: true
---

# 搜索引擎 #

今天阅读了几篇关于信息检索（搜索引擎）的文献，记录下来。 

一、WEB信息检索的综述  
  
<pre><code>
----用----------户------------
	|	|	|   ↓
	|	|	|  信息检索agent
	|	|	↓   ↓
	|	|  元搜索引擎
	|	↓     ↓
	|   搜索引擎    目录
    ↓      ↓   
----互-----联-----网---------

	web信息检索系统分层。上层建立在下层基础之上。
</code></pre>

WEB信息检索技术包括以下技术：  
爬虫，分析器，索引器，检索器，用户接口。  
>WEB---Robtos---分析器---索引器--索引数据库---检索器---用户接口---用户


**爬虫**(Robot,spider,crawler,wander)：从某一链接开始，**遍历并下载**web文档，将每一个遍历的链接加入超链队列(或堆栈)中。
  
+ Q1:**如何遍历整个新浪新闻页面**:news.sina.com.cn？   

**分析器**：对robots下载的文档分析（分词、过滤、转换）。  
>从header中分词？还是全文分词。  
>禁用词表（stop list)用语去除出现平率很高的词条  
>单词转换，词缀去除(stemming),同一转换(conflation)  

+ Q2:**如何从某篇新闻中分析出正文部分**？ 


**索引器**： 对文档的索引（模型有：倒排、矢量空间、概率模型等）  
在[xapian的笔记中](http://myersguo.github.io/2013/04/07/xapian_step1.html)，xapian的索引模型为：(documentID,terms,values),即保存文档D的ID，分词term的位置、权重，分类(values）。  


+ 传统的图书馆的文献资料是怎么索引的？  



**检索器**：用户发送查询请求，检索器到索引数据库中找到相关文档。  

**用户接口**：向用户提供的可视化输出的结果(标题、摘要、超链接等)界面。  

     
二、一个简单的网络爬虫  

使用[python](http://python.org/)爬网页方便快捷。我也学习一下。     

流程：   
搜集页面所有超链接——>将超链接存放到链接数据库中(linked database)——>下载链接文档。   
<pre><code>
下载文档          URL解析器       URL去重  
    ↓		      ↓            ↓  
HTML文档     ————————————————>     URL列表  
</code></pre>  

1) **基础知识**  
先看一段网页代码:   


	<html><head><title> hello,world</title></head>
	<body>
	<a href="/next.html">next page</a>
	<a href="/home.html">back home</a>
	</body>
	</html>  


这是一串简单的字符串。现在我们需要提取其中的链接。   
使用python的[正则表达式](http://docs.python.org/2/howto/regex.html)工具[re](http://docs.python.org/2/library/re.html)来提取符号条件的内容。


链接的模式是：
`<a  href = 链接内容 > 标题 </a>`
	
用正则表达式就是：

	(<a href=".*">.*</a>)?  
	 p = re.compile(r'(<a href=)".*">.*</a>',re.DOTALL);

取到html地址之后，就可以下载文档了。

python的扩展urllib2提供了一些操作URL的工具函数。  

比如下载某个URL的网页内容：  
urllib2.urlopen(url,[data][,timeout]);
参数：
   url: 网址字符串或请求对象.
   eg:
   
	urllib2.urlopen('http://www.baidu.com');  
	req = urllib2.Request('http://www.baidu.com');

返回值：
urllib2.open的返回值是一个文件流。

下载网页:

	i = 1;
	filename = 'myfile'+str(i);
	file = open(filename,"w");
	
	f  = urllib2.urlopen(url);
	file.write(f.read());
	file.close();`


2)**[最终测试小码](#test_code)**  

>作者有点懒，还没有补充完整
>
>  



**后记**：  

今天，简单记了点关于搜索引擎的笔记。在记录和实践的过程中发现以下几点：  
>1.我对python语言不熟悉，待我熟读python后补全[最终代码](#test_code)  
>2.正则表达式是计算机技术的基本功。我打算在未来一个月内抽时间写一篇正则的学习笔记。  
>3.搜索引擎的学习是一个长期的过程，本文粗浅的笔记，just a beginning.

have a good day。
明天，我将翻译android的设计标准。我很期待。  


参考资料：  
[1]web信息检索研究进展.王继成等.2001年2月.计算机研究与发展.第38卷第2期.  
[2][python正则表达式指南](http://wiki.ubuntu.com.cn/Python%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E6%93%8D%E4%BD%9C%E6%8C%87%E5%8D%97)  
[3][python正则表达式零宽断言](http://www.cnblogs.com/xiehuiqi220/archive/2009/02/06/1385481.html)
