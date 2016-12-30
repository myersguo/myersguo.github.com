---
layout: wp
title: linux system limit
---


今天遇到一个报错：   

```
max file descriptors [65535] for  process is too low, increase to at least [65536]
max number of threads [1024] for user myersguo is too low, increase to at least [2048]
max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

这里我们要关注以下几个文件：  



我们先来看这个文件`/etc/security/limits.conf` :  
  

```
# /etc/security/limits.conf
#
#Each line describes a limit for a user in the form:
#
#<domain>        <type>  <item>  <value>
#
#Where:
#<domain> can be:
#        - an user name
#        - a group name, with @group syntax
#        - the wildcard *, for default entry
#        - the wildcard %, can be also used with %group syntax,
#                 for maxlogin limit
#
#<type> can have the two values:
#        - "soft" for enforcing the soft limits
#        - "hard" for enforcing hard limits
#
#<item> can be one of the following:
#        - core - limits the core file size (KB)
#        - data - max data size (KB)
#        - fsize - maximum filesize (KB)
#        - memlock - max locked-in-memory address space (KB)
#        - nofile - max number of open files
#        - rss - max resident set size (KB)
#        - stack - max stack size (KB)
#        - cpu - max CPU time (MIN)
#        - nproc - max number of processes
#        - as - address space limit (KB)
#        - maxlogins - max number of logins for this user
#        - maxsyslogins - max number of logins on the system
#        - priority - the priority to run user process with
#        - locks - max number of file locks the user can hold
#        - sigpending - max number of pending signals
#        - msgqueue - max memory used by POSIX message queues (bytes)
#        - nice - max nice priority allowed to raise to values: [-20, 19]
#        - rtprio - max realtime priority
#
#<domain>      <type>  <item>         <value>
#
```


#### Soft Limits & Hard Limits ####   

在[Oracle](https://docs.oracle.com/cd/E19455-01/805-7229/sysresquotas-1/index.html)上看到的一个解释，超级棒：  

>You can set both soft and hard limits. The system will not allow a user to exceed his or her hard limit. However, a system administrator may set a soft limit (sometimes referred to as a quota) which can be temporarily exceeded by the user. The soft limit must be less than the hard limit.
>  
>Once the user exceeds the soft limit, a timer begins. While the timer is ticking, the user is allowed to operate above the soft limit but cannot exceed the hard limit. Once the user goes below the soft limit, the timer gets reset. However, if the user\s usage remains above the soft limit when the timer expires, the soft limit is enforced as a hard limit. By default, the soft limit timer is seven days.


即，soft limit限制超过后，会有一个计时器，计时器结束后，仍然超过，就KILL，否则OK。  


### 三个限制 ###   

#### max file descriptor ####    

`maximum number of open files`

***文件描述符(file descriptor)***是资源(文件,输入输出流,网络socket连接,etc)的标识。没打开一个文件描述符，系统都要分配一定内存，因此必须限制文件描述符的数量。   

查看进程ID 3020的文件描述符:  

```
lsof -p 3020  | wc -l
ls -a /proc/3020/fd  | wc -l

```

更新/etc/security/limits  

```
 *       soft    nofile      65536
 *       hard    nofile      65536
```

这样就解决了file descriptor的限制。   

ulimit -a 查看一下   

```
*       soft    nproc       2048
*       hard    nproc       2048
```

或者:`ulimit -u 2048`设置每个用户最大进程数(max user process).  

同样的设置虚拟内存：  

```
sysctl -w vm.max_map_count=300000
cat /proc/sys/vm/max_map_count
```
我们可以从`cat /proc/1920/maps  | more`来查看内存映射。   



遇到的问题是已经解决了，但仿佛留给了我们更多的疑问。   




### 参考资料 ###  

[https://docs.oracle.com/cd/E19455-01/805-7229/sysresquotas-1/index.html](https://docs.oracle.com/cd/E19455-01/805-7229/sysresquotas-1/index.html)  
[https://en.wikipedia.org/wiki/File_descriptor](https://en.wikipedia.org/wiki/File_descriptor)  
[http://duartes.org/gustavo/blog/post/how-the-kernel-manages-your-memory/](http://duartes.org/gustavo/blog/post/how-the-kernel-manages-your-memory/)  

