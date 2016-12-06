---
layout: wp
title: nginx pathinfo
---

先看nginx的fastcgi配置`/home/work/app/nginx/fastcgi_params`      


```

fastcgi_param  QUERY_STRING       $query_string;
fastcgi_param  REQUEST_METHOD     $request_method;
fastcgi_param  CONTENT_TYPE       $content_type;
fastcgi_param  CONTENT_LENGTH     $content_length;

fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  REQUEST_URI        $request_uri;
fastcgi_param  DOCUMENT_URI       $document_uri;
fastcgi_param  DOCUMENT_ROOT      $document_root;
fastcgi_param  SERVER_PROTOCOL    $server_protocol;
fastcgi_param  HTTPS              $https if_not_empty;

fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
fastcgi_param  SERVER_SOFTWARE    nginx/$nginx_version;

fastcgi_param  REMOTE_ADDR        $remote_addr;
fastcgi_param  REMOTE_PORT        $remote_port;
fastcgi_param  SERVER_ADDR        $server_addr;
fastcgi_param  SERVER_PORT        $server_port;
fastcgi_param  SERVER_NAME        $server_name;

# PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param  REDIRECT_STATUS    200;
```


我们写个php测试文件,index.php:   

```
echo 'PATH_INFO '.$_SERVER['PATH_INFO'].PHP_EOL;
echo 'ORIG_PATH_INFO '.$_SERVER['ORIG_PATH_INFO'].PHP_EOL;
echo 'REQUEST_URI '.$_SERVER['REQUEST_URI'].PHP_EOL;
echo 'SCRIPT_FILENAME '.$_SERVER['SCRIPT_FILENAME'].PHP_EOL;
echo 'SCRIPT_NAME '.$_SERVER['SCRIPT_FILENAME'].PHP_EOL;
echo 'QUERY_STRING '.$_SERVER['QUERY_STRING'].PHP_EOL;
echo 'REQUEST_METHOD '.$_SERVER['REQUEST_METHOD'].PHP_EOL;
echo 'REMOTE_ADDR '.$_SERVER['REMOTE_ADDR'].PHP_EOL;
echo 'REQUEST_METHOD '.$_SERVER['REQUEST_METHOD'].PHP_EOL;
echo 'SERVER_ADDR '.$_SERVER['SERVER_ADDR'].PHP_EOL;
echo 'SERVER_PORT '.$_SERVER['SERVER_PORT'].PHP_EOL;
exit;
```

结果：   

```
PATH_INFO 
ORIG_PATH_INFO 
REQUEST_URI /test/test.php
SCRIPT_FILENAME /home/work/data/www/mi_com_myers/test/test.php
SCRIPT_NAME /home/work/data/www/mi_com_myers/test/test.php
QUERY_STRING 
REQUEST_METHOD GET
REMOTE_ADDR 192.168.235.1
REQUEST_METHOD GET
SERVER_ADDR 192.168.235.128
SERVER_PORT 8084
```

再看yii的nginx配置：   


```
  #首先rewrite到index.php
  if (!-e $request_filename){
        rewrite ^/(.+)$ /index.php break;
  }


 location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        set $real_script_name $fastcgi_script_name;
        if ($fastcgi_script_name ~ "^(.+?\\.php)(/.+)$") {
            set $real_script_name $1;
            set $path_info $2;
        }
        include        fastcgi_params;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_param SCRIPT_NAME $real_script_name;
        fastcgi_param PATH_INFO $path_info;

    }
```

这样之后,fastcgi_script_name永远都是index.php。


```
PATH_INFO 
ORIG_PATH_INFO 
REQUEST_URI /index.php/users/app.php
SCRIPT_FILENAME /home/work/data/www/test/index.php
SCRIPT_NAME /home/work/data/www/test/index.php
QUERY_STRING 
REQUEST_METHOD GET
REMOTE_ADDR 192.168.235.1
REQUEST_METHOD GET
SERVER_ADDR 192.168.235.128
SERVER_PORT 80
```

####  fastcgi_script_name ####    

>request URI or, if a URI ends with a slash, request URI with an index file name configured by the fastcgi_index directive appended to it. This variable can be used to set the SCRIPT_FILENAME and PATH_TRANSLATED parameters that determine the script name in PHP. For example, for the “/info/” request with the following directives
>fastcgi_index index.php;
>fastcgi_param SCRIPT_FILENAME /home/www/scripts/php$fastcgi_script_name;
>the SCRIPT_FILENAME parameter will be equal to “/home/www/scripts/php/info/index.php”.



### 参考资料 ####   

[http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html](http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html)   





