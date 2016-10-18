---
layout: wp
title: 重新鼓捣一个全新的开发环境  
---

我在我的机器

```
cat /etc/centos-release
CentOS release 6.4 (Final)
uname -a
2.6.32-358.el6.x86_64），
```

而docker[官方](https://docs.docker.com/engine/installation/linux/centos/)写的prerequistites:  

```
Docker requires a 64-bit OS and version 3.10 or higher of the Linux kernel.
```

我在linux2.6版本上折腾docker,一堆的问题。决定迁移到centos 7上。   

### 安装centos ### 


```
vagrant init centos/7
vagrant up --provider virtualbox
```

### 初始化私人配置 ###  

执行ifconfig发现 command not found,ifconfig在/sbin/ifconfig，
更改profile

```php
if [ "$EUID" = "0" ]; then
    pathmunge /sbin
    pathmunge /usr/sbin
    pathmunge /usr/local/sbin
else
    pathmunge /usr/local/sbin after
    pathmunge /usr/sbin after
    pathmunge /sbin after
fi
```

若ifconfig都没有，只能重新安装。。

```
yum install net-tools -y
yum install bridge-utils -y
```

开启ssh密码认证： 

```
sed -i "s/PasswordAuthentication yes/PasswordAuthentication no/g" /etc/ssh/sshd_config
```

由于virtualbox需要设置两个网卡(nat,host-only)，而不像vmware的nat模式直接就可以访问主机和外网了。   

#### 初始化工作目录 ####   

```
su -
visudo
添加work到sudoer
work    ALL=(ALL)       PASSWD:ALL

useradd work
su work
mkdir  /home/work/data /home/work/app /home/work/logs /home/work/logs/applogs  /home/work/logs/php
```

#### 初始化我的LNMP ####   

```
yum install libc-client -y
yum install libxslt-devel -y
yum install postgresql-devel -y
yum install -y wget

wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm  
rpm -ivh epel-release-latest-7.noarch.rpm 
yum install freetds freetds-devel

yum install -y libmcrypt libmcrypt-devel mcrypt mhash
yum install -y libpng12-devel

唉，直接把开发编译环境安装了:
yum group install -y "Development Tools"
yum install -y libjpeg libjpeg-devel

wget https://forensics.cert.org/cert-forensics-tools-release-el7.rpm
rpm -Uvh cert-forensics-tools-release-el7.rpm 
yum --enablerepo=forensics install libiconv

sudo yum install python-setuptools
sudo easy_install pip
sudo pip install supervisor
sudo echo_supervisord_conf

```
ps:

```
写到这里，还没写完，而且，老罗的发布会还没结束：
2016.10.18 22:36  

```

如果提示没有找到，可以添加其他的repo来，因为我添加的是公司的就不贴出来了。  


````
直接copy原来系统的过来或编译安装,略
```




#### samba 


####

vim

```


```





