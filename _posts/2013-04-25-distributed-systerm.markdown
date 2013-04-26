---
layout: wp
title:   分布式计算一小瞥  
comments: true
blogindex: false
---

## 分布式计算  
![one does not simply](http://qph.is.quoracdn.net/main-qimg-62d9c6ba870554801feb758c27f98c7d)  
### 缘起  
我总希望在谈论某个问题时，某君说：  
“**Let me give you an example,so you can understand**”    

昨晚，突然想搞清楚[分布式计算](http://en.wikipedia.org/wiki/Distributed_computing)的基本概念。或因分布式实时流计算的[twitter storm](https://github.com/nathanmarz/storm)和[yahoo s4](http://incubator.apache.org/s4/)让我求知欲大增。我只能简单的写下我的分布式计算的学习笔记(too simple,too stupid)。    


### 引子    
1960s,操作系统引入并发技术；1970s,分布式系统应用于局域网；1982年 分布式计算研讨会(Symposium on Principles of Distributed Computing ,PODC)召开；可以从[PBOC](http://www.podc.org/)会议中的主要论文了解分布式计算的历史和研究进展。  

分布式研究从1970年，人们想象着世界的计算机相互连接的情境，到考虑现实中不同时钟、不同结构的计算机共同完成任务，同时防范某个计算机挂掉，研究范围包括：建立网络和网络环境、通信协议，分布式应用等。[Appraising Two Decades of Distributed Computing Theory Research,点击下载](http://cs-www.cs.yale.edu/homes/fischer/pubs/appraising.pdf)回顾了从1982年到2002年分布式计算领域的主要研究内容，我对其进行了摘要：   

#### 拜占庭将军问题[Byzantine fault tolerance](http://research.microsoft.com/en-us/um/people/lamport/pubs/byz.pdf)   
它是信息容错研究问题。在分布式系统中，分布的计算节点可能出现系统错误（如系统崩溃，消息接受失败，消息发送失败等）、计算错误（执行请求错误，发送错误结果等）等等。当" Byzantine failure"发生就会有不可预知的错误，因此只能设定有一定的容错("Byzantine fault tolerance")。  

#### 早期的研究  
最早的分布式研究是对分布式框架和协议的研究。  
+ **Natural abstract models**自然抽象模型 
+ **Meaningful performance measures**性能评估    
+ **Comprehensive and comprehensible problem specifications**综合的问题描述    
+ **Rules of composition that enable large systems to be buildt from simple,well-understood componets and that enable complex systems to be decomposed into simpler,more easily understood components**分布式组件的规则  


#### 研究内容  
+ Global coordination 协调同步  
+ Process step timing and scheduling 时钟和调度的过程  
+ Communication system 通信系统  
+ Fault tolerance 容错处理  

### 从头开始   

#### 问题  
假如有一个非常长的乘法A1xB1xC1x......,怎么做？  
若不考虑溢出，可以把乘法分成N个乘法的式子。然后分别交给M台计算机同时运算，最终计算出所有乘法的结果。   

1. 如果判断每台计算机的计算结果是否正确？要知道千里之堤溃于蚁穴。分散制必然需要监管方可放心。    
2. 计算机崩溃掉了怎么处理？   

#### 思考  

最简单的分布式架构就是单一的C/S模式，客户端反馈服务器端的请求，进行任务分解。  

在[quora](http://www.quora.com/)上有个小伙和我有了同样的问题：  
>[This morning, I had a whim of building a Distributed File System from scratch to learn the intricacies of building such a system, what would be your timeline if you were to work on such project?](http://www.quora.com/Distributed-Systems/This-morning-I-had-a-whim-of-building-a-Distributed-File-System-from-scratch-to-learn-the-intricacies-of-building-such-a-system-what-would-be-your-timeline-if-you-were-to-work-on-such-project)。  

Jeff-Darcy[回答](http://www.quora.com/Distributed-Systems/This-morning-I-had-a-whim-of-building-a-Distributed-File-System-from-scratch-to-learn-the-intricacies-of-building-such-a-system-what-would-be-your-timeline-if-you-were-to-work-on-such-project/answer/Jeff-Darcy)道：    
>I've been a tech lead for a couple of distributed filesystems. The first one ended up taking over two years with up to twenty developers.  The one I work on now is several years old, and currently has twice that many developers.  Or look at Ceph[1] which, despite having some of the most brilliant people on the planet working on it, still isn't "all there" yet.  Of course, if your goal is a research-level rather than production level of completeness, then timelines will be shorter, but any kind of filesystem development (even local filesystems) is still a monumental pain in the posterior.  

分布式计算是一个巨大的工程，需要不断的积累。同时，分布式计算相关知识有很多学科基础知识，必须清楚。比如：
计算机的容错实现，网络通信，文件系统等等。  

学习，任重道远。但，跬步意味着千里的可能。  
  
引申阅读：  
[1] [http://www.podc.org/](http://www.podc.org/)    
[2][Reading List](http://courses.csail.mit.edu/6.852/08/handouts/handout3.pdf)    
[3][Appraising Two Decades of Distributed Computing Theory Research](http://cs-www.cs.yale.edu/homes/fischer/pubs/appraising.pdf)  
[4][http://en.wikipedia.org/wiki/Category:Distributed_computing_architecture](http://en.wikipedia.org/wiki/Category:Distributed_computing_architecture)  
