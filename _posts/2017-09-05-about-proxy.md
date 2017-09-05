---
layout: wp
title: use proxy
---

在测试数据准备阶段，有一种方式可以使用 proxy 来收集请求接口，在 case 执行时回放 case请求。同时，可以在 case 执行时统计http request的覆盖情况。   

这里介绍几个好用的 proxy 工具。    

### [mitmproxy](https://github.com/mitmproxy/mitmproxy) ###  

[安装](http://docs.mitmproxy.org/en/stable/install.html),mac 上直接:   

```
brew install mitmproxy
```

使用:   

```
mitmproxy -p 8090
```

用web工具:   

```
mitmweb --web-port 8091 -p 8090
```

设置手机代理端口为8090，然后使用手机，可以看到代理的 http 请求。非常赞。     

### [anyproxy](http://anyproxy.io/cn.html) ###


install:   

```
npm install -g anyproxy@beta 
```

执行:`anyproxy`   

默认 proxy 端口8001, 默认web管理端口8002   


###  nginx ###

用nginx 作为一个正向代理服务器(forward proxy)，将请求转发到真正的服务器, 配置如下:   


```
server {
    listen  7999;

    location / {
        resolver 8.8.8.8;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_pass $scheme://$http_host$uri$is_args$args;
    }
}
```

但是, nginx 作为 server 不支持 https正向代理。这个比较尴尬。    


