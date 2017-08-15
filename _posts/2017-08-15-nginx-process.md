---
layout: wp
title: nginx 处理流程
---

今天遇到两个关于 nginx 的问题：

1. nginx 是怎么处理一个请求的？  
2. nginx 配置了一堆upstream 是怎么选择的?   

#### 第一个问题 ####



```
server {
	return 404;
}
server {
	listen	80;
    server_name mytest;
    location / {
        return 200 'hello,world';
        add_header Content-Type text/plain;
    }
}
```

当我直接访问 `localhost`的时候，没有返回 `hello,world`， 而需要访问 mytest 才OK， 但是（注意，我说但是），我添加了 header:   

```
Host: mytest
```
之后，访问没有问题。那么 [How nginx processes a request](http://nginx.org/en/docs/http/request_processing.html)?   


>In this configuration nginx tests only the request’s **header** field **“Host”** to determine which server the request should be **routed** to. If its value does not match any **server name**, or the request does not contain this header field at all, then nginx will route the request to the default server for this port. In the configuration above, the default server is the first one — which is nginx’s standard default behaviour. It can also be set explicitly which server should be default, with the default_server parameter in the listen directive:


翻译一下就是，nginx 会根据 请求头中的 host来选择匹配 `server_name`的路由。再看下面的例子:  

```
server {
    listen 10.2.207.145:80;
    server_name mytest2;
    location / {
        return 200 'hello,world';
        add_header Content-Type text/plain;
    }
}
```

nginx 会首先检查listen的ip,port是否对应，然后再检查server_name,

[server_name](http://nginx.org/en/docs/http/server_names.html)的查找方式安装一下顺序：  

* 准确的名称  
* * 开头的最长通配符   
* * 结尾的最长通配符  
* 第一个配配的正则表达式   

#### 第二个问题 ####

upstream 是怎么路由选择服务器的？  

```
upstream backend {
    server backend1.example.com       weight=5;
    server backend2.example.com:8080;
    server unix:/tmp/backend3;

    server backup1.example.com:8080   backup;
    server backup2.example.com:8080   backup;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

>By default, requests are distributed between the servers using a weighted round-robin balancing method. In the above example, each 7 requests will be distributed as follows: 5 requests go to backend1.example.com and one request to each of the second and third servers. If an error occurs during communication with a server, the request will be passed to the next server, and so on until all of the functioning servers will be tried. If a successful response could not be obtained from any of the servers, the client will receive the result of the communication with the last server.

即，默认nginx基于**加权轮转**的方法发到各个请求服务器。上面的例子，假如来了7个用户请求，其中5个走到backend1,1个走到backend2,1个走到backend3。加入其中一个请求失败了，会走轮转到下一个服务。**当主服务都不可用**时，轮转到backup服务。  

还有其他策略吗？   

**ip_hash**: 请求基于客户端的IP地址在服务器间进行分发。 IPv4地址的前三个字节或者IPv6的整个地址，会被用来作为一个散列key。 这种方法可以确保从同一个客户端过来的请求，会被传给同一台服务器。除了当服务器被认为不可用的时候，这些客户端的请求会被传给其他服务器，而且很有可能也是同一台服务器。


### 参考资料 ###

[http://nginx.org/en/docs/http/request_processing.html](http://nginx.org/en/docs/http/request_processing.html)   
[http://tengine.taobao.org/nginx_docs/cn/docs/http/request_processing.html#mixed_name_ip_based_servers](http://tengine.taobao.org/nginx_docs/cn/docs/http/request_processing.html#mixed_name_ip_based_servers)   

