---
layout: wp
title: xapian的笔记|step 1 of xapian
comments: true
---
荀子・劝学篇

>**吾尝终日而思矣，不如须臾之所学也**。吾尝跂而望矣，不如登高之博见也。登高而招，臂非加长也，而见者远；顺风而呼，声非加疾也，而闻者彰。假舆马者，非利足也，而致千里；假舟楫者，非能水也，而绝江河。君子生非异也，善假于物也。    
>
>积土成山，风雨兴焉；积水成渊，蛟龙生焉；积善成德，而神明自得，圣心备焉。故**不积跬步，无以至千里；不积小流，无以成江海**。骐骥一跃，不能十步；驽马十驾，功在不舍。锲而舍之，朽木不折；锲而不舍，金石可镂。蚓无爪牙之利，筋骨之强，上食埃土，下饮黄泉，用心一也。蟹六跪而二螯，非蛇鳝之穴无可寄托者，用心躁也。是故无冥冥之志者，无昭昭之明；无惛惛之事者，无赫赫之功。行衢道者不至，事两君者不容。目不能两视而明，耳不能两听而聪。螣蛇无足而飞，鼫鼠五技而穷。 


#xapian阅读笔记  

正文：  
阅读开源代码从那里开始呢？  
我的步骤是:  
1. 如果源码提供有DOC文档，先看DOC文档。  
2. 将源码跑起来。  
3. 仔细阅读API接口，用几个例子做测试。  
 
本文是我边读边写的笔记。记录凌乱之处，希望自己和看到它们的读者都能够体谅一下。  
  
一、xapain的安装。  
具体可参考：[Installing Xapian](http://xapian.org/docs/install.html)  
安装过程可能出现包缺失的错误，比如uuid包。可google之。  

二、xapian 理论  
1) Databases（数据库）  
数据库是搜索的基础，所有待搜索的文档都要放到数据库中，这个过程叫做索引:
>The process of putting documents into the database is usually referred to as indexing.  

xapian的数据库不是建立在『关系数据库』(relational database)上，而是在数据后端中用特征文件系统存储信息。其存放的是term到documents的映射。文件系统采用的是典型的B+树结构（B+-tree structure）  
数据后端使用[版本并发控制机制](http://en.wikipedia.org/wiki/Multiversion_concurrency_control)进行『单写多读』:  
>all the backends only support a single writer existing at a given time; Multiple concurrent readers are supported.  

数据库被读时，相当于打开一个快照，但xapian限制，只能打开一个数据库的两个快照。  
>Currently Xapian’s disk based backends have a limitation to their multi-version concurrency implementation - specifically, at most two versions can exist concurrently.  

数据后端可建在不同的机器上，可被复制到其他机器上，支持数据冗余和负载均衡。
> Xapian  has special support for replicating databases to multiple machines.this can be useful for redundancy and load-balancing purposes.



2) Documents(文档）  
搜索引擎返回的结果称为文档（Documents)。 
> A document in Xapian is simply an item which is returned by a search.  


文档的形式可以是多种。一般用用一段/篇文本表示。    
文档在数据库(Databases)中用唯一的32bit整数ID表示。因此一个Database可保存40亿份文档。  
文档由三部分组成：data,terms,values.  
<pre><code> 
关系：                   
			documents		
	|		|		|		|
	id		data	terms	values
term—————m:n——documents
一个term可指向包含其的多个document；
一个document包含多个term。
 </code> 
</pre>

data.文档的data部分存放是，想要展示给用户的数据经过zlib压缩后的二进制数据。  
terms.文档的terms是从经过**规整策略**从文档抽取出来的**分词**单元。**是对document的描述。**每个term出现的位置和频率被记录在数据库中。  
> **within document frequency**:same word will occur multiple times in a piece of text  
>**the term frequency** is the number of documents that a particular term occurs in  
>**the collection frequency** is the total number of times that term occurs    

values.用来对搜索进行排序和分类的值。比如一个视频可能具有的value slot为：年份，演员，导演等等。  

3）查询    

+ xapian支持简单的文本查询（在数据库中找到包含term的文档）    
+ 支持通配符查询，如，wild*,匹配whildcard,wildcat,...
+ 支持逻辑运算符：AND,OR,AND_NOT 
+ 支持在结果中查询： FILTER
+ 支持按照某一类别(value)排序  
+ 支持前缀搜索，如:author:william 
 
排序方法(Ranked matches)：  

xapian按照document的权重(weight)得分排序，其排序方法乃大名鼎鼎的[BM25](http://xapian.org/docs/bm25.html)(match set 25).    


参考资料：  
[1]http://getting-started-with-xapian.readthedocs.org/  
[2]http://xapian.org/docs/  
[3]http://www.oschina.net/question/11_7280  
[4]多版本并发控制：http://en.wikipedia.org/wiki/Multiversion_concurrency_control  
[5]BM25算法：http://xapian.org/docs/bm25.html  


