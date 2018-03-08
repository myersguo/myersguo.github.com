---
layout: wp
title: About Celery
comments: true
---

我们先看官方的一个[例子](http://celery.readthedocs.io/en/latest/getting-started/next-steps.html):   

proj/celery.py:   


```
from __future__ import absolute_import, unicode_literals
from celery import Celery

app = Celery('proj',
             broker='redis://127.0.0.1:6379/0',
             backend='redis://127.0.0.1:6379',
             include=['proj.tasks'])

app.conf.update(
    task_routes = {
        'proj.tasks.add': {'queue': 'cn'},
        'proj.tasks.add': {'queue': 'i18n'},
    },
)

if __name__ == '__main__':
    app.start()
```

proj/tasks.py   

```
from __future__ import absolute_import, unicode_literals
from .celery import app


@app.task
def add(x, y):
    return x + y
```

proj/send_tasks.py   

```
from .tasks import add

if __name__ == '__main__':
    add.apply_async((10, 5), queue='cn')

    add.apply_async((1, 2), queue='i18n')
```

在 proj 的同一级执行：   

`celery -A proj worker -l info -Q cn`

```
celery@myersguo v4.1.0 (latentcall)

Darwin-17.4.0-x86_64-i386-64bit 2018-02-24 15:06:23

[config]
.> app:         proj:0x108a69750
.> transport:   redis://127.0.0.1:6379/0
.> results:     redis://127.0.0.1:6379/
.> concurrency: 4 (prefork)
.> task events: OFF (enable -E to monitor tasks in this worker)

[queues]
.> i18n             exchange=i18n(direct) key=i18n


[tasks]
  . proj.tasks.add

[2018-02-24 15:06:23,452: INFO/MainProcess] Connected to redis://127.0.0.1:6379/0
[2018-02-24 15:06:23,460: INFO/MainProcess] mingle: searching for neighbors
[2018-02-24 15:06:24,476: INFO/MainProcess] mingle: all alone
[2018-02-24 15:06:24,485: INFO/MainProcess] celery@myersguo ready.
```


如果要在后台运行（生产环境），执行：  
`celery multi start w1 -A proj -l info`    

执行发送任务:   

`python -m proj.send_tasks`  

结果：  

```
[2018-02-24 15:07:25,283: INFO/MainProcess] Received task: proj.tasks.add[614194cf-dd33-4cce-95a2-901d295ffc97]
[2018-02-24 15:07:25,293: INFO/ForkPoolWorker-2] Task proj.tasks.add[614194cf-dd33-4cce-95a2-901d295ffc97] succeeded in 0.00814947500476s: 3
```

### 架构 ###  

[producers,consumers, brokers](http://celery.readthedocs.io/en/latest/userguide/routing.html#producers-consumers-and-brokers)   

>The client sending messages is typically called a publisher, or a producer, while the entity receiving messages is called a consumer.
>  
>The broker is the message server, routing messages from producers to consumers.
>  

[exchange,queues,routeing key](http://celery.readthedocs.io/en/latest/userguide/routing.html#exchanges-queues-and-routing-keys)   

>1.Messages发送到exchanges  
>2.Exchange路由message到1个或多个队列 
>3.队列中消息被consumer消费后从队列中删除   

>1.创建 exchange
>2.创建 queue
>3.绑定 queue 到 exchange。消息从exchange路由到 queue由Exchange types决定，有direct, topic, fanout and headers四种。   
>direct类型默认，queue 通过确定的 routing key 接收消息.Topic exchanges支持通配符表达式的 routing key的消息。



### 常用命令 ###

节点监控: celery -A proj status    


### 多项目使用 ###

如果项目A只是想发送一个任务，传递一些参数，然项目 B执行，producer 怎么做?   

```
# 发送任务
from __future__ import absolute_import, unicode_literals
from celery import Celery
app = Celery('proj',
             broker='redis://127.0.0.1:6379/0',
             backend='redis://127.0.0.1:6379')

print app.send_task('proj.tasks.add', args=(1,2), queue='i18n')
```

[task](http://celery.readthedocs.io/en/latest/userguide/tasks.html)   

>A task is a class that can be created out of any callable. It performs dual roles in that it defines both what happens when a task is called (sends a message), and what happens when a worker receives that message.     

在本文中的task，从redis中看一下`LRANGE I18N 0 1`:  

```
{
  "body": "W1sxLCAyXSwge30sIHsiY2hvcmQiOiBudWxsLCAiY2FsbGJhY2tzIjogbnVsbCwgImVycmJhY2tzIjogbnVsbCwgImNoYWluIjogbnVsbH1d",
  "headers": {
    "origin": "gen37699@myersguo",
    "root_id": "24eeef0a-58b5-4233-bca9-0cdf5fb22b14",
    "expires": null,
    "id": "24eeef0a-58b5-4233-bca9-0cdf5fb22b14",
    "kwargsrepr": "{}",
    "lang": "py",
    "retries": 0,
    "task": "proj.tasks.add",
    "group": null,
    "timelimit": [
      null,
      null
    ],
    "parent_id": null,
    "argsrepr": "(1, 2)",
    "eta": null
  },
  "content-type": "application/json",
  "properties": {
    "priority": 0,
    "body_encoding": "base64",
    "correlation_id": "24eeef0a-58b5-4233-bca9-0cdf5fb22b14",
    "reply_to": "85e6cd16-37e2-3dcc-a73d-cc6a04b8311d",
    "delivery_info": {
      "routing_key": "i18n",
      "exchange": ""
    },
    "delivery_mode": 2,
    "delivery_tag": "fa1db653-13a4-4a8d-bddb-4cd4a8c2df0b"
  },
  "content-encoding": "utf-8"
}
```

### 重试 ###

```
@app.task(autoretry_for=(Exception,),max_retries=3,default_retry_delay=1 * 6)
def add(x, y):
    raise Exception('hi')
    return x + y
```

### 序列化 ###

默认task使用`json`格式，还可以使用 pickle, YAML and msgpack等。   

### ruote ###

eg:   

```
task_routes = ([
    ('feed.tasks.*', {'queue': 'feeds'}),
    ('web.tasks.*', {'queue': 'web'}),
    (re.compile(r'(video|image)\.tasks\..*'), {'queue': 'media'}),
],)
```
A queue named “video” will be created with the following settings:

```
{'exchange': 'video',
 'exchange_type': 'direct',
 'routing_key': 'video'
```





### 多机房配置 ###

在开头的例子中，针对`i18n`和`cn`两个机房设置了不同的`queue`来处理，那不同机房的不同配置怎么处理呢？可以从**环境变量**中读取配置。   

```
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery

#: Set default configuration module name
os.environ.setdefault('CELERY_CONFIG_MODULE', 'proj.celeryconfig')

app = Celery()
app.config_from_envvar('CELERY_CONFIG_MODULE')
```

默认从proj.celeryconfig获取任务，如果是其他机房，可以根据机房的环境变量读另外的配置.   

```
cat proj/celeryconfig.py
broker_url ='redis://127.0.0.1:6379/0'
result_backend='redis://127.0.0.1:6379'
imports = ('proj.tasks',)

task_routes = {
        'proj.tasks.add': {'queue': 'cn'},
        'proj.tasks.add': {'queue': 'i18n'},
}
```

### 原理篇 ###

我们执行`celery -A proj worker -l info -Q cn`的流程：  

# celery.bin.base
execute_from_commandline->setup_app_from_commandline->handle_argv(celery.bin.celery)->execute->run_from_argv(celery.bin.worker)->__call__(celery.bin.base)->run(celery.bin.worker)->start->start(celery.bootsteps)->on_start(celery.apps.worker)->start(celery.bootsteps)->start(celery.consurrency.base)->  
start(celery.worker.consumer.consumer)   



#### app ####

path: celery/app/base.py   

amqp: 高级消息队列协议的实现，使用Kombu作为消息的发送和接受客户端。

发送任务:   

```
    def send_task(self, name, args=None, kwargs=None, countdown=None,
                  eta=None, task_id=None, producer=None, connection=None,
                  router=None, result_cls=None, expires=None,
                  publisher=None, link=None, link_error=None,
                  add_to_parent=True, reply_to=None, **options):
        task_id = task_id or uuid()
        producer = producer or publisher  # XXX compat
        router = router or self.amqp.router
        conf = self.conf
        if conf.CELERY_ALWAYS_EAGER:  # pragma: no cover
            warnings.warn(AlwaysEagerIgnored(
                'CELERY_ALWAYS_EAGER has no effect on send_task',
            ), stacklevel=2)
        options = router.route(options, name, args, kwargs)
        if connection:
            producer = self.amqp.TaskProducer(connection)
        with self.producer_or_acquire(producer) as P:
            self.backend.on_task_call(P, task_id)
            task_id = P.publish_task(
                name, args, kwargs, countdown=countdown, eta=eta,
                task_id=task_id, expires=expires,
                callbacks=maybe_list(link), errbacks=maybe_list(link_error),
                reply_to=reply_to or self.oid, **options
            )
        result = (result_cls or self.AsyncResult)(task_id)
        if add_to_parent:
            parent = get_current_worker_task()
            if parent:
                parent.add_trail(result)
        return result
```


### worker ###

bin path: celery.bin.worker  
class path: celery.apps.worker  
step: celery.worker.components 执行步骤   
consumer: celery.worker.consumer   

多线程:celery.concurrency.*


### C_FORCE_ROOT ###

如果 celery 在root帐号下启动，并且支持`pickle`序列化，那么需要设置`C_FORCE_ROOT`环境变量为`true`才可以运行，检查：  


```
def check_privileges(accept_content):
    uid = os.getuid() if hasattr(os, 'getuid') else 65535
    gid = os.getgid() if hasattr(os, 'getgid') else 65535
    euid = os.geteuid() if hasattr(os, 'geteuid') else 65535
    egid = os.getegid() if hasattr(os, 'getegid') else 65535

    if hasattr(os, 'fchown'):
        if not all(hasattr(os, attr)
                   for attr in ['getuid', 'getgid', 'geteuid', 'getegid']):
            raise AssertionError('suspicious platform, contact support')

    if not uid or not gid or not euid or not egid:
        if ('pickle' in accept_content or
                'application/x-python-serialize' in accept_content):
            if not C_FORCE_ROOT:
                try:
                    print(ROOT_DISALLOWED.format(
                        uid=uid, euid=euid, gid=gid, egid=egid,
                    ), file=sys.stderr)
                finally:
                    os._exit(1)
        warnings.warn(RuntimeWarning(ROOT_DISCOURAGED.format(
            uid=uid, euid=euid, gid=gid, egid=egid,
        )))
```
