---
title: uwsgi 一瞥
layout: wp
comments: true
---

### uwsgi 的简单使用###

uwsgi --help 输出它的一些命令参数。我们看一下常见的参数：  

--http-socket: 绑定 UNIX/TCP socket 端口，并使用 http 协议(eg: 0.0.0.0:1234)    

--protocol: 默认绑定的协议   
--processes/--workers: 开启的进程数量   
--harakiri: 进程响应超时设置   
--xmlconfig/--xml: 从 xml文件中获取配置   
--ini: 从ini文件中获取配置   
--daemonize: 后台运行   
--spooler: spooler 目录 

--logto:日志文件    
--wsgi-file: 加载 wsgi 文件   
--module/--wsgi: 加载 uwsgi module    
--callable: 默认的 uwsgi callable 名称   
 

ok, 我们先写一个 uwsgi 的入口(bootstrap)函数：   
```
#bootstrap.py
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    response = ["Hello", "World"]
    return  response

```

在不使用 uwsgi 时，我们先用 [wsgiref](https://docs.python.org/2/library/wsgiref.html#module-wsgiref.simple_server)来实现看下一个简单的应用怎么写：  


```
httpd = make_server('', 9090, application)
print "Serving on port 9090..."
httpd.serve_forever()
```

上面的 `application` 和 `wsgi server` 共同构成了 wsgi 的规范（即，一个 [wsgi](http://wsgi.tutorial.codepoint.net/application-interface)是一个连接规范，just an interface specification by which server and application communicate）   


我们使用 uwsgi 来启动：   
`uwsgi --http :9090 --wsgi-file bootstrap.py --master --processes 4 --threads 2`    

我们还可以通过配置 xml/ini 文件来启动：  


```
#uwsgi.xml
<uwsgi>
	<http-socket>0.0.0.0:9090</http-socket>
	<module>bootstrap</module>
	<workers>4</workers>
	<threads>2</threads>
</uwsgi>
```

or in ini file   

```
[uwsgi]
http-socket = 0.0.0.0:9090
wsgi-file = bootstrap.py
workers = 4
threads = 2
```


#### 入口 ####

我们来看上面写的 application, 参数有两个 `env` 和 `start_response`, 这两个是什么东西呢？  


`env`: 一个大 DICT 类型，里面包括一次请求的环境：  

```
SCRIPT_NAME
REQUEST_METHOD
PATH_INFO
REQUEST_URI
HTTP_USER_AGENT
SERVER_NAME/SERVER_PORT/
REMOTE_ADDR
...
```

`start_response` 是一个 [uwsgi_split](https://github.com/unbit/uwsgi/blob/master/plugins/python/python_plugin.c#L227) built-in function.  

### uwsgi 定时器 ###

写一个定时任务:   

```
from uwsgidecorators import *

@timer(3)
def three_seconds(signum):
    print("3 seconds elapsed")
```

加载这个定时任务：  

`uwsgi --http :9090 --wsgi-file bootstrap.py --master --processes 4 --threads 2 --import mytasks.py`  

ok, 我们能看到每个 3秒一次输出。    
或者[这样](http://uwsgi-docs-zh.readthedocs.io/zh_CN/latest/Signals.html)：  

```
import uwsgi

def hello_timer(num):
        print "2 seconds elapsed, signal %d raised" % num

def oneshot_timer(num):
        print "40 seconds elapsed, signal %d raised. You will never see me again." % num


uwsgi.register_signal(26, "worker", hello_timer)
uwsgi.register_signal(30, "", oneshot_timer)

uwsgi.add_timer(26, 2) # never-ending timer every 2 seconds
uwsgi.add_rb_timer(30, 40, 1) # one shot rb timer after 40 seconds
```

定时器有哪些使用场景呢？   

* 定期 dump/上报数据  
* 定期 更新内存数据...  



### uwsgi 源码一瞥 ###

uwsgi 是一个用 c 写的 webserver, 经典的 master/worker 进程模式。   

>一开始进入main函数，启动这个就是主进程了，uwsgi_setup函数（主进程）里面针对选项参数做一些处理，执行环境设置，执行一些hook，语言环境初始化（python），如果没有设置延迟加载app，则app在主进程加载；如果设置了延迟加载，则在每一个worker进程中都会加载一次。uwsgi_setup函数还执行了插件的初始化（eg http、python：http是gateway类型的插件，这种插件是向外暴露http服务的，python的 requests 类型的插件，用来服务请求的）、tcp socket的绑定与监听（这个是指http与work之间的通信，且它的端口是自动产生的）。最后uwsgi主进程fork了指定worker进程用来接收(accept)请求。虽然在setup中就fork了子进程，但是现在还没有开始accept。     
>主进程执行部分是一个无限循环，他可以执行特定的hook以及接收信号等，总之是用来管理worker进程以及一些定时或者事件触发任务。worker部分：注册型号处理函数，执行一些hook，循环接收(accept)请求。在启动woker时可以根据--threads参数指定要产生的线程个数，否则只在当前进程启动一个线程。这些线程循环接收请求并处理。   
>worker中接收请求的函数wsgi_req_accept有一个锁：thunder_lock，这个锁用来串行化accept，防止“惊群”现象：主进程绑定并监听socket，然后调用fork，在各个子进程进行accept。无论任何时候，只要有一个连接尝试连接，所有的子进程都将被唤醒，但只有一个会连接成功，其他的会得到一个EAGAIN的错误，导致巨大的CPU资源浪费，如果在进程中使用线程，这个问题被再度放大。一个解决方法是串行化accept，在accept前防止一个锁。    



[init](https://github.com/unbit/uwsgi/blob/master/core/uwsgi.c#L2107): 

```
#ifdef UWSGI_AS_SHARED_LIBRARY
int uwsgi_init(int argc, char *argv[], char *envp[]) {
#else
int main(int argc, char *argv[], char *envp[]) {
#endif
	uwsgi_setup(argc, argv, envp);
	return uwsgi_run();
}
```

[setup](https://github.com/unbit/uwsgi/blob/master/core/uwsgi.c#L2127):  

```
void uwsgi_setup(int argc, char *argv[], char *envp[]) {
    init_magic_table(uwsgi.magic_table);

	// initialize schemes
	uwsgi_setup_schemes();

	// initialize the clock
	uwsgi_register_clock(&uwsgi_unix_clock);
	uwsgi_set_clock("unix");

	// fallback config
	atexit(uwsgi_fallback_config);
	// manage/flush logs
	atexit(uwsgi_flush_logs);
	// clear sockets, pidfiles...
	atexit(vacuum);
	// call user scripts
	atexit(uwsgi_exec_atexit);
	// call plugin specific exit hooks
	atexit(uwsgi_plugins_atexit);
    ...
    // call here to allows plugin to override hooks
	uwsgi_register_base_hooks();
	uwsgi_register_logchunks();
	uwsgi_log_encoders_register_embedded();
    //initialize embedded plugins
    UWSGI_LOAD_EMBEDDED_PLUGINS

	// register base metrics (so plugins can override them)
	uwsgi_metrics_collectors_setup();
    ...
    uwsgi_start((void *) uwsgi.argv);
}
```

[uwsgi_start](https://github.com/unbit/uwsgi/blob/master/core/uwsgi.c#L3435):  

```
uwsgi_hooks_run(uwsgi.hook_in_jail, "in-jail", 1);
// initialize the exception handlers
	uwsgi_exception_setup_handlers();

	// initialize socket protocols (do it after caching !!!)
	uwsgi_protocols_register();
	if (uwsgi.has_threads) {
		if (uwsgi.threads > 1)
			uwsgi.current_wsgi_req = threaded_current_wsgi_req;
		(void) pthread_attr_init(&uwsgi.threads_attr);
		if (uwsgi.threads_stacksize) {
			if (pthread_attr_setstacksize(&uwsgi.threads_attr, uwsgi.threads_stacksize * 1024) == 0) {
				uwsgi_log("threads stack size set to %luk\n", (unsigned long) uwsgi.threads_stacksize);
			}
			else {
				uwsgi_log("!!! unable to set requested threads stacksize !!!\n");
			}
		}

		pthread_mutex_init(&uwsgi.lock_static, NULL);

		// again check for workers/sockets...
		if (uwsgi.sockets || uwsgi.master_process || uwsgi.no_server || uwsgi.command_mode || uwsgi.loop) {
			for (i = 0; i < 256; i++) {
				if (uwsgi.p[i]->enable_threads)
					uwsgi.p[i]->enable_threads();
			}
		}
	}
	uwsgi_worker_run();
```

[uwsgi_worker_run](https://github.com/unbit/uwsgi/blob/master/core/uwsgi.c#L3578):

```
if (uwsgi.lazy || uwsgi.lazy_apps) {
		uwsgi_init_all_apps();
	}

```

### uwsgi 插件实现 ###

>uwsgi使用的是插件化的开发方式。启动时，加载相关的若干个插件（比如python请求插件），并对这些插件进行初始化；运行时，根据输入，调用相应的插件的钩子函数，来完成处理。uwsgi只需要完成主流程和通用功能，而将具体实现交给插件去做。  
>uwsgi采用的是经典的 单master/多worker 并发模型。master负责信号处理，初始化，创建并管理worker进程，以及绑定监听sockets。worker的每个线程，处在这样的一个循环中：accept -> recv -> handle request -> send response。   


[python plugin](https://github.com/unbit/uwsgi/blob/master/plugins/python/python_plugin.c#L2047)  


我们看官方的[插件例子](https://github.com/unbit/uwsgi-docs/blob/master/tutorials/WritingPlugins.rst)  


```
#include <uwsgi.h>

static int foo_init() {
      uwsgi_log("Hello World\n");
      return 0;
}

struct uwsgi_plugin foobar_plugin = {
        .name = "foobar",
        .init = foo_init,
};
```

编译： 

python uwsgiconfig.py --plugin foobar.c  

启动：  

uwsgi --plugin ./foobar_plugin.so --http :9090 --wsgi-file bootstrap.py  


ok,这样就可以看到加载了插件的 uwsgi   



### 其他 ###

官方例子中[c python module](https://gist.github.com/myersguo/ea12aa987c888431ad097bb18ff06b64)   


```
gcc spammify.c -fPIC -I/usr/include/python2.7  -shared -o spam.so
```


### 参考 ### 

http://wsgi.tutorial.codepoint.net/application-interface  
https://docs.python.org/2/extending/extending.html  
https://github.com/enthought/Python-2.7.3/blob/master/Demo/embed/demo.c   
https://www.zouyesheng.com/python-module-c.html  
https://www.letiantian.me/2015-09-10-understand-python-wsgi/   
http://uwsgi-docs-zh.readthedocs.io/zh_CN/latest/PythonDecorators.html
http://uwsgi-docs.readthedocs.io/en/latest/PythonDecorators.html   


