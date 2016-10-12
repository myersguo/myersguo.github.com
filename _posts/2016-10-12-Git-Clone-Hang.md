---
layout: wp
title: git clone hang up
---
今天在一台新及其上配置git ssh key之后，执行git clone xxx,发现一直显示：  

>Cloning into 

没有任何报错，初步怀疑是端口号禁用？执行 lsof -i(or sudo netstat -lptu)，没发现，经过巨长时间等待，出现提示: 

```
ssh: connect to host xxx.xxx.xxx.xxx port 22: Connection timed out
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.  
```

连接超时，但是我可以ping通啊，为什么连接超时？难道服务挂了？但为什么其他机器没有问题，这时我抱着试试看的态度打开了host文件,vi /etc/hosts,发现，是谁把git地址绑定到一个错误的ip上了。。。。   


