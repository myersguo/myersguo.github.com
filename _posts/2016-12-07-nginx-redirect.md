---
layout: wp
title: nginx redirect
---

nginx的转发模块`ngx_http_rewrite_module`包含以下指令:

>break
>if
>return
>rewrite
>rewrite_log
>set
>uninitialized_variable_warn


### break ###   

break指令用来停止当前的ngx_http_rewrite_module指令集合(current set of ngx_http_rewrite_module directives)。   

比如：    

```
if ($slow) {
    limit_rate 10k;
    break;
}
```

### if ###

条件判断，如果满足条件，括号内的指令将被执行。条件可以为：  

>变量名：true or false
>比较运算： = or !=
>正则表达式： ~(区分大小写匹配case-sensitive matching)or ~*(不区分大小写匹配case-insensitive matching) 
>-f: 判断文件是否存在 
>-d：判断文件，文件夹，符号连接是否存在
>-x: 检查文件是否可执行  


例如：  

```
if ($http_user_agent ~ MSIE) {
    rewrite ^(.*)$ /msie/$1 break;
}

if ($http_cookie ~* "id=([^;]+)(?:;|$)") {
    set $id $1;
}

if ($request_method = POST) {
    return 405;
}

if ($slow) {
    limit_rate 10k;
}

if ($invalid_referer) {
    return 403;
}
```


### return ###   

停止处理并发挥特殊的http code 给客户端。   


```
return (301 | 302 | 303 | 307) url;
```



```
return (1xx | 2xx | 4xx | 5xx) ["text"];
```

### rewrite ###   

```
rewrite regex URL [flag];
```

如果请求的URI满足regex，则URI被替换。如果替换的URI是http或https开头，则停止以后处理并返回给客户端。   

`flag`的可选值为：  

>last:停止当前的ngx_http_rewrite_module指令并以替换的URI来查找新的location。   
>break:停止当前的ngx_http_rewrite_module指令****集合****。
>redirect:返回302.
>permanent:返回永久的转发:301 code.


注意：`last`指令会重新查找新的location,而break会跳出当前的location or if direct。  
因此，当使用于location内部中时，last会跳出后重新匹配。

```
location /download/ {
    rewrite ^(/download/.*)/media/(.*)\..*$ $1/mp3/$2.mp3 break;
    rewrite ^(/download/.*)/audio/(.*)\..*$ $1/mp3/$2.ra  break;
    return  403;
}
```

假如你访问URL:/download/a/media/a.mp3。
上面将匹配：`/download/a/mp3/a.mp3`。   

如果，将break更换为last:   


```
location /download/ {
    rewrite ^(/download/.*)/media/(.*)\..*$ $1/mp3/$2.mp3 last;
    rewrite ^(/download/.*)/audio/(.*)\..*$ $1/mp3/$2.ra  last;
    return  403;
}
```

那么过程就变成了： 

```
/download/a/media/a.mp3-->/download/a/mp3/a.mp3-->
/download/a/mp3/a.mp3-->403
```

### set ###  

设置变量。eg:  

```
 set $is_mobile "pc";
 if ( $http_user_agent ~* "(Android|iPhone|iPod|Symbian|IEMobile|BlackBerry)"){
       set $is_mobile "mobile";
 }
```
 


### 应用 ###   

例子来自于:[nginx blog](https://www.nginx.com/blog/creating-nginx-rewrite-rules/):   


```
server {
    listen 80;
    server_name www.old-name.com old-name.com;
    return 301 $scheme://www.new-name.com$request_uri;
    #return 301 $scheme://www.new-name.conm;
    #rewrite ^ $scheme://www.new-name.com$request_uri permanent;
    #rewrite ^/(.*)$ $scheme://www.new-name.com/$1 permanent;

}
```
add or remove www

```
# add 'www'
server {
    listen 80;
    listen 443 ssl;
    server_name domain.com;
    return 301 $scheme://www.domain.com$request_uri;
    #rewrite ^(.*)$ $scheme://www.domain.com$1 permanent;
}

# remove 'www'
server {
    listen 80;
    listen 443 ssl;
    server_name www.domain.com;
    return 301 $scheme://domain.com$request_uri;
}
```

默认转发：   

```
server {
    listen 80 default_server;
    listen 443 ssl default_server;
    server_name _;
    return 301 $scheme://www.domain.com;
}
```

强制HTTPS：   

```
server {
    listen 80;
    server_name www.domain.com;
    return 301 https://www.domain.com$request_uri;
}
```

或者:   

```
# NOT RECOMMENDED
if ($scheme != "https") {
    rewrite ^ https://www.mydomain.com$uri permanent;
}
```
这种，需要额外处理，不推荐使用。    


```
location / {
    try_files $uri $uri/ /index.php?$args;
}
```

移除不支持文件：  

```
location ~ \.(aspx|php|jsp|cgi)$ {
    return 410;
    #deny all;
}
```

自定义路由：   

```
rewrite ^/listings/(.*)$ /listing.html?listing=$1 last;

```








### 参考资料  ###   

[https://www.nginx.com/blog/creating-nginx-rewrite-rules/](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)   
[http://nginx.org/en/docs/http/ngx_http_rewrite_module.html](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html)  


