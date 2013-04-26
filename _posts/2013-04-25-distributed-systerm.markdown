---
layout: wp
title:   什么是分布式计算  
comments: true
blogindex: false
---

## 分布式计算  

### 缘起  
昨晚，突然想搞清楚[分布式计算](http://en.wikipedia.org/wiki/Distributed_computing)的基本概念。或因分布式实时流计算的[twitter storm](https://github.com/nathanmarz/storm)和[yahoo s4](http://incubator.apache.org/s4/)让我求知欲大增。我只能简单的写下我的分布式计算的学习笔记(too simple,too stupid)。  

### 历史  
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




to be continued


  
引申阅读：  
[1] [http://www.podc.org/](http://www.podc.org/)  
[2][Reading List](http://courses.csail.mit.edu/6.852/08/handouts/handout3.pdf)  
[3][Appraising Two Decades of Distributed Computing Theory Research](http://cs-www.cs.yale.edu/homes/fischer/pubs/appraising.pdf)
[4]