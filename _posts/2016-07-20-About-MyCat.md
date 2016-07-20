---
layout: wp
title: About Mycat
---

### Install  

 wget https://raw.githubusercontent.com/MyCATApache/Mycat-download/master/1.5-RELEASE/Mycat-server-1.5.1-RELEASE-20160705210037-linux.tar.gz   
 tar xvf Mycat-server-1.5.1-RELEASE-20160705210037-linux.tar.gz && cd mycat   
 useradd mycat   
 chown -R mycat:mycat ./mycat    

配置文件：  

schema.xml: 
    定义mycat的数据节点(dataNode)和数据库(dataHost)连接信息
server.xml:  
   定义mycat的连接用户(user)和系统属性:系统端口和管理端口等  . 
rule.xml:  
    定义DB TABLE拆分规则  


