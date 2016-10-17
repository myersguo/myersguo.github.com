---
layout: wp
title: docker 私有镜像搭建
---

1. install docker  

从[官网]找到安装教程：  

```
$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF

```
我本地使用的是：  

```
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
```

先卸载本地docker:  
```
yum -y docker-engine  
``` 



执行安装： 

```
sudo yum -y install docker-engine   
```  


开机启动：  

```
sudo systemctl enable docker.service  
```  

启动docker 服务：  

```
sudo systemctl start docker  
```

安装失败，使用docker -d 出现错误提示：   

```
docker: relocation error: docker: symbol dm_task_get_info_with_deferred_remove, version Base not defined in file libdevmapper.so.1.02 with link time reference

```

google到[docker issues]中通过升级device-mapper-level解决了([linux device mapper]机制)

```
yum install device-mapper-devel     
```

查看docker 的信息：  

```
 sudo docker info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-253:0-803726-pool
 Pool Blocksize: 65.54 kB
 Backing Filesystem: extfs
 Data file: /dev/loop0
 Metadata file: /dev/loop1
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Data Space Available: 4.417 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Metadata Space Available: 2.147 GB
 Udev Sync Supported: true
 Deferred Removal Enabled: false
 Data loop file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
 Library Version: 1.02.117-RHEL6 (2016-08-15)
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 2.6.32-358.el6.x86_64
Operating System: <unknown>
CPUs: 4
Total Memory: 996.1 MiB
Name: vagrant.localdomain
ID: A4YR:RJB3:A7A5:HSCU:ZNQV:FYBE:QJPI:PUM3:5UTM:A37L:OHFK:VEE  

```

2. 搭建docker 私有仓库   
docker 的[dtr]架构可以简单了解一下。  

这里我们使用docker[官方的仓库来搭建]：  

```
sudo docker run -d -p 5000:5000 --name registry registry:2.0.1
```

结果提示我：   

```
Status: Downloaded newer image for registry:2.0.1
3380f0b3718fec49190b517388b736bdc64d89fa2d690658d2f74c81e63f8af2
Error response from daemon: Cannot start container 3380f0b3718fec49190b517388b736bdc64d89fa2d690658d2f74c81e63f8af2: no such file or directory   
```
查看docker日志：  


```
tail -f /var/log/docker 
time="2016-10-14T15:36:06.761464890+08:00" level=info msg="POST /v1.19/containers/create" 
time="2016-10-14T15:36:07.214793986+08:00" level=info msg="POST /v1.19/containers/ee33fb5d4651c04a139ea8725d9faf9cf7626aa12700875907dddad7f6c37e7f/attach?stderr=1&stdout=1&stream=1" 
time="2016-10-14T15:36:07.220437210+08:00" level=info msg="POST /v1.19/containers/ee33fb5d4651c04a139ea8725d9faf9cf7626aa12700875907dddad7f6c37e7f/start" 
time="2016-10-14T15:36:07.434821004+08:00" level=error msg="leaving endpoint failed: a container has already joined the endpoint" 
time="2016-10-14T15:36:08.573283642+08:00" level=error msg="Handler for POST /containers/{name:.*}/start returned error: Cannot start container ee33fb5d4651c04a139ea8725d9faf9cf7626aa12700875907dddad7f6c37e7f: no such file or directory" 
time="2016-10-14T15:36:08.573444334+08:00" level=error msg="HTTP Error" err="Cannot start container ee33fb5d4651c04a139ea8725d9faf9cf7626aa12700875907dddad7f6c37e7f: no such file or directory" statusCode=404   

```

why?

```
docker version
Client version: 1.7.1
Client API version: 1.19
Go version (client): go1.4.2
Git commit (client): 786b29d
OS/Arch (client): linux/amd64
Server version: 1.7.1
Server API version: 1.19
Go version (server): go1.4.2
Git commit (server): 786b29d
OS/Arch (server): linux/amd64

uname -a
Linux vagrant.localdomain 2.6.32-358.el6.x86_64 #1 SMP Fri Feb 22 00:31:26 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
```

认证问题？  

```
cat /var/log/dmesg | grep DENIED   
cat /var/log/audit/audit.log | grep DENIED   
```


都找不出认证问题。   


### 暂时木有解决  ###  
有后续的解决方案了，再来补充一下。   














### 参考资料  

[官网]: https://docs.docker.com/engine/installation/linux/centos/  
[docker issues]: https://github.com/docker/docker/issues/12108    
[linux device mapper]: http://www.ibm.com/developerworks/cn/linux/l-devmapper/  
[dtr]: https://docs.docker.com/docker-trusted-registry/architecture/  
[官方的仓库来搭建]: https://docs.docker.com/registry/deploying/  
 


