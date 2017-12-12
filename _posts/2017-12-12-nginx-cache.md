---
layout: wp
title: nginx 服务降级
---

假如一个`http`服务要做降级，可以从`nginx`入手：   

1) nginx 添加cache   

nginx 可以对整个接口内容做cache,假如一个服务在一定时间内返回是固定的，可以直接在nginx层做cache。cache key 一般是`host+uri+arg`，如果 args 中有时间戳之类的变量，需要做一些特殊处理（去掉时间戳参数），举个例子：   

```
proxy_cache_path /tmp/nginx/cache levels=1:2 keys_zone=cache:10m max_size=512m inactive=7d;
server {
    listen 1234;

    set $new_args $args;
    if ($new_args ~ (.*)(?:&|^)_timestmap=[^&]*(.*)) {
       set $new_args $1$2;
    }

    location / {
    	proxy_connect_timeout 1s;
    	proxy_send_timeout    6s;
    	proxy_read_timeout    10s;
    	proxy_set_header Host $host;
    	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_cache cache;
        proxy_cache_key $host$uri$is_args$new_args;
        proxy_ignore_headers X-Accel-Expires Expires Cache-Control Set-Cookie;#坑
        proxy_cache_valid 200 10m; #缓存10分钟
        proxy_pass  http://myapi;
    }
}

upstream myapi {
    server 127.0.0.1:9090 max_fails=3 fail_timeout=5s;
    keepalive 32;
}
```

刚配置的时候遇到缓存不生效的问题，原因为cache会判断 http header 中是否有 cache-control 或 Cookie设置，如果这些是可以忽略的，那设置 `proxy_ignore_headers X-Accel-Expires Expires Cache-Control Set-Cookie;` 就会忽略这些变化

ok, 我们benchmark 一下:

```
with cache: 2808.09 [#/sec] (mean)   
no cache: 111.65 [#/sec] (mean)
```
20 倍的性能提升!   

2) 降级   

[未完待续]



