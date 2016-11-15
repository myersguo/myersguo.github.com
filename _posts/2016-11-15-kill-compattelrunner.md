---
layout: wp
title: CompatTelRunner--Windows Disk I/O 100%
---

开机之后这个进程占用了100%的CPU和磁盘，简直不能忍。  
What's this? 

好像是win7加入的一个个功能，后来win10又添加了。有网友大呼:  

>Win 10: the terrible CompatTelRunner is back! now the terrible CompatTelRunner is starting with Windows, and it is using far too much CPU, from 0% up to 80%, and ever so slowing the use of Windows. After it has done its job, it will close until next Windows launch. 


似乎是一个诊断、收集程序。这让我想起昨儿看的文章说淘宝的用户跟踪JS占所有JS的一半。。。。   

kill it:   

Task Scheduler  - Microsoft - Windows - Application Experience - right click on each task for: DISABLE


 

