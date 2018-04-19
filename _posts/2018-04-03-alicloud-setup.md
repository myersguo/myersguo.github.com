---
layout: wp
title: Ali Cloud Set Up
comments: true
---

```
useradd -m -d /home/myersguo -s /bin/bash myersguo
passwd myersguo
sudo usermod -a -G sudo myersguo

sed -i "s/PasswordAuthentication yes/PasswordAuthentication no/g" /etc/ssh/sshd_config

mkdir  /home/myersguo/data /home/myersguo/app /home/myersguo/logs /home/myersguo/logs/applogs  /home/myersguo/logs/php

sudo apt-get update
apt-get install net-tools
apt-get install libpcre3-dev libssl-dev perl make build-essential curl
```

镜像地址：

```
# /etc/apt/sources.list
deb http://mirrors.cloud.aliyuncs.com/debian/ stretch main contrib non-free
deb http://mirrors.cloud.aliyuncs.com/debian/ stretch-updates main contrib non-free
deb http://mirrors.cloud.aliyuncs.com/debian/ stretch-proposed-updates main non-free contrib
deb http://mirrors.cloud.aliyuncs.com/debian/ stretch-backports main non-free contrib
## Major bug fix updates produced after the final release of the
## distribution.
deb-src http://mirrors.cloud.aliyuncs.com/debian/ stretch-updates main contrib non-free
deb-src http://mirrors.cloud.aliyuncs.com/debian/ stretch main contrib non-free
deb-src http://mirrors.cloud.aliyuncs.com/debian/ stretch-proposed-updates main contrib non-free
deb-src http://mirrors.cloud.aliyuncs.com/debian/ stretch-backports main contrib non-free
deb http://mirrors.cloud.aliyuncs.com/debian-security/ stretch/updates main non-free contrib
deb-src http://mirrors.cloud.aliyuncs.com/debian-security/ stretch/updates main non-free contrib
```


### mysql ###

```
apt-get install -y mysql-server mysql-client
mysql_secure_installation

/etc/mysql/my.cnf
/etc/systemd/system/mysql.service
/etc/systemd/system/mysqld.service
/etc/systemd/system/multi-user.target.wants/mariadb.service

```

### git ###

```
sudo apt-get install git-core
```

### nginx/openresty ###

```
apt-get install libpcre3-dev \
    libssl-dev perl make build-essential curl

PATH=/usr/local/openresty/nginx/sbin:$PATH

apt-get install -y nginx-full
lsof -i :80
```


### setup my blog ###

```
sudo apt-get install ruby-full
git clone https://github.com/myersguo/myersguo.github.com.git
```
参考[例子](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/), 生成静态文件.启动 nginx 配置:   

``` 
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    server {
        listen 80;
        root /home/myersguo/app/myersguo.github.com/_site;
        include /usr/local/openresty/nginx/conf/mime.types;
        default_type application/octet-stream;
        index index.html index.htm;
        location / {
            
        }
    }
}

```

启动： `nginx -p /home/myersguo -c conf/nginx.conf`   


