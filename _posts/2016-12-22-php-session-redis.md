---
layout: wp
title: php session to redis
---

之前面试的时候，经常问别人，session,cookie是什么，怎么存储的？   
有一个人给我胡说了半天，说session的所有信息都通存在http的header里面，说着说着，又变成了存在http body里面。。。   

好，今天谈谈这个。  


>Sessions are a simple way to store data for individual users against a unique session ID. This can be used to persist state information between page requests. Session IDs are normally ***sent to the browser via session cookies*** and the ID is used to retrieve existing session data. The absence of an ID or session cookie lets PHP know to create a new session, and generate a new session ID.


我们看到php的[session.name](https://github.com/php/php-src/blob/PHP-5.3/ext/session/session.c#L793)初始值是"PHPSESSID"。   

>The session name references the name of the session, which is used in cookies and URLs (e.g. PHPSESSID). It should contain only alphanumeric characters; it should be short and descriptive (i.e. for users with enabled cookie warnings). If name is specified, the name of the current session is changed to its value.   



session name 存储的值，就是session id,即：   
`COOKIE(PHPSESSID)  = SESSION ID`   


我们保存用户登录信息，一般保存在SESSION里面，默认的SESSION是使用PHP[内部文件](http://php.net/manual/en/session.examples.basic.php)的存储的：   

>By default, PHP uses the internal files save handler which is set by session.save_handler. This saves session data on the server at the location specified by the session.save_path configuration directive.  


当我们多级部署时,必须使用其他方式存储session，一般存储在db,cache中，举个redis session的例子：   

```
<?php                                                                                                                            

class RedisSessionCache /*implements HandlerInterface */{
    protected $redis;

    public function __construct($redis) {
        $this->redis = $redis;
        @session_set_save_handler(array($this,'open'),array($this,'close'),array($this,'read'),array($this,'write'),array($this,'
destroy'),array($this,'gc'));

    }
    public function close() {
        return true;
    }

    public function  gc ( int $maxlifetime ) {
        return true;
    }
    public function  open ( $save_path, $_name ) {
        return true;
    }
    public function read ( $_id ) {
        $data = $this->redis->get($_id);
        return $data === false ? '' : $data;
    }
    public function write ( $_id , $_data ) {
        $this->redis->set($_id, $_data);
    }
    public function destroy( $_id ) {
        $this->redis->delete($_id);
    }



}
$redis = new Redis();
$redis->connect('localhost', '6379');
$redis->auth('test123');
$session = new RedisSessionCache($redis);
session_start();
$_SESSION['test'.$_SERVER['REMOTE_ADDR']]=123;
var_dump($_SESSION);
```

访问之后，我们看到COOKIE `PHPSESSID`的值是`qm921l9jtm9nvq1g6ijisbon91`,连接redis获取该值：   

```
telnet 127.0.0.1 6379
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
auth test123
+OK
get qm921l9jtm9nvq1g6ijisbon91
$24
test192.168.235.1|i:123;
```

这样就把session保存在redis中了，一个问题，怎么设置session name 的COOKIE过期时间呢？     

`session_set_cookie_params`来`Set the session cookie parameters`.   




[http://php.net/manual/en/session.customhandler.php](http://php.net/manual/en/session.customhandler.php)   
[https://en.wikipedia.org/wiki/Session_fixation](https://en.wikipedia.org/wiki/Session_fixation)  














