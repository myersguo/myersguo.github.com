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




#### samba  ####   


```
yum install samba samba-client samba-swat
sudo smbpasswd -a work

```


####

vim

```
yum install -y  vim
git clone https://github.com/myersguo/vim.myersguo.git
cd vim.myersguo
/bin/sh ./build.sh
mv .vim ~/
mv .vimrc ~/

```

#### my bashrc ####

```
alias vi=vim
alias cp='cp -i'
alias l.='ls -d .* --color=auto'
alias ll='ls -ahl --color=auto'
alias ls='ls --color=auto'
alias diff='diff -u'
alias grep='grep --color '

#alias ps='ps -axjf '
#alias git diff='git diff --cached'
#alias git diff='git diff --staged'

randpw(){ < /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c${1:-16};echo;}
#export PS1="[\u@\h \W$(__git_ps1)\[$WHITE\]]\$ "
#PS1="\[$BLUE\]\u\[$YELLOW\]\[$YELLOW\]\w\[\033[m\]\[$MAGENTA\]\$(__git_ps1)\[$WHITE\]\$ "
#PS1="\[$GREEN\]\t\[$RED\]-\[$BLUE\]\u\[$YELLOW\]\[$YELLOW\]\w\[\033[m\]\[$MAGENTA\]\$(__git_ps1)\[$WHITE\]\$ "
#PS1='\e[33;1m\u@\h: \e[1;37m\W\e[31m$(parse_git_branch " (%s)")\e[1;37m\$ '

if [ ! -S ~/.ssh/ssh_auth_sock ]; then
    eval `ssh-agent`
    ln -sf "$SSH_AUTH_SOCK" ~/.ssh/ssh_auth_sock
fi
export SSH_AUTH_SOCK=~/.ssh/ssh_auth_sock
ssh-add -l | grep "The agent has no identities" &&  ssh-add ~/.ssh/id_rsa_myersguo_github


```
#### mysql ####  

这里使用percona版本的mysql.  

```
groupadd mysql
useradd mysql -g mysql

yum install cmake gcc gcc-c++ libaio libaio-devel automake autoconf bzr \
  bison libtool ncurses-devel zlib-devel libgcrypt-devel


yum install:
yum install http://www.percona.com/downloads/percona-release/redhat/0.1-3/percona-release-0.1-3.noarch.rpm
yum install Percona-Server-server-57
查查mysql安装到哪里了？
rpm -ql Percona-Server-server-57
/usr/bin
/etc
/usr/lib
/usr/lib64
/usr/sbin
/usr/share


编译安装：
cd /home/work/data/www
git clone https://github.com/percona/percona-server.git
cd percona-server
git checkout 5.6
git submodule init
git submodule upate
cmake .
make dist
cmake . -DCMAKE_BUILD_TYPE=RelWithDebInfo -DBUILD_CONFIG=mysql_release -DFEATURE_SET=community -DWITH_EMBEDDED_SERVER=OFF
make install
```

mysql的启动：   
/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid  
初始化密码：

看日志能看到初始密码:

```
sudo grep 'temporary password' /var/log/mysqld.log
sudo mysql_secure_installation
```

```
SET GLOBAL validate_password_policy=LOW;
GRANT ALL PRIVILEGES ON mi_scm2.* TO 'myersguo'@'%' IDENTIFIED BY '密码';

```

#### redis #### 

```
mkdir /home/work/app/redis
mkdir /home/work/app/redis/etc
wget http://download.redis.io/releases/redis-3.2.4.tar.gz
tar xvf redis-3.2.4.tar.gz 
进入目录
make PREFIX=/home/work/app/redis
make PREFIX=/home/work/app/redis install

```
服务脚本:

```
cp redis.conf /home/work/app/redis/etc/6379.conf
cp utils/redis_init_script /etc/init.d/redis
```
前面内容修改为：

```
#!/bin/sh
### BEGIN INIT INFO
# Provides:          redis
# Required-Start:    $remote_fs $network
# Required-Stop:     $remote_fs $network
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: starts redis
# Description:       starts redis
### END INIT INFO

REDISPORT=6379
REDIS_DIR=/home/work/app/redis
EXEC=${REDIS_DIR}/bin/redis-server
CLIEXEC=${REDIS_DIR}/bin/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="${REDIS_DIR}/etc/${REDISPORT}.conf"

在最后加上restart:
    restart):
        $0 stop
        $0 start                                                                                                              
        ;;

```


#### 其他安装 #### 


```
yum install -y usbutils
yum install -y cmake ncurses ncurses-devel
```
#### 问题 ####  
我启动mysql的时候报需要权限： 
```
service mysql start
Redirecting to /bin/systemctl start  mysql.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: root
Password: 
==== AUTHENTICATION COMPLETE ===
```



#### 结语 ####  

我发现我被坑了，vagrant官方的centos7 box是个core版本的，只有最小的内核相关的。其他常用的一些包都木有啊。   







