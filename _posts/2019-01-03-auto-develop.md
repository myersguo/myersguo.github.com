---
layout: wp
title: auto develop
---

我们通过 [gin](https://github.com/gin-gonic/gin/blob/master/.travis.yml) 这个项目来看开源项目的开发与构建。   

### auto build ###
登录到 [travis](https://docs.travis-ci.com/user/tutorial/) 打开 auto build, 在项目根路径下添加 [.travis.yml](https://github.com/gin-gonic/gin/blob/master/.travis.yml), 然后等着每次 [build](https://travis-ci.org/gin-gonic/gin)即可。   

从[这篇文章](https://www.paperplanes.de/2013/10/18/the-smallest-distributed-system.html) 和[这里](https://github.com/travis-ci/travis-ci)我们可以粗略看出， travis 的基本组成：   

```
Once a build request has been:

accepted by Listener,
approved and configured by Gatekeeper,
and its jobs have been scheduled by Scheduler,
they will be picked by a Worker which will execute the build Bash script as generated by the build script compiler).
```

[travis-web](https://github.com/travis-ci/travis-web) travis web client   
[api](https://github.com/travis-ci/travis-api) travis 的核心逻辑层，提供运行服务的 api   
[travis-build](https://github.com/travis-ci/travis-build)将travis.yml 转换成 build.sh,提供给 worker 执行   
[hub](https://github.com/travis-ci/travis-hub) 从其他 app 接收消息，然后通知其他app处理。比如，它通知task 模块， build 开始、完成事件。   
[travis-listener](https://github.com/travis-ci/travis-listener)接收 github 等的消息，然后发送到 mq  
[travis-logs](https://github.com/travis-ci/travis-logs)Travis Logs processes log updates which are streamed from Travis Worker instances via RabbitMQ. The log parts are streamed via Pusher to the web client (Travis Web) and added to the database.   
[travis-tasks](https://github.com/travis-ci/travis-tasks)接收任务的消息并发送通知   
[job-board](https://github.com/travis-ci/job-board) API 接口（代替异步的 mq) 来将构建任务传给 worker。  
[schedule](https://github.com/travis-ci/travis-scheduler)接收触发请求(eg 来自github),创建一个构建任务(build record),
[worker](https://github.com/travis-ci/worker) 负责执行 ci job。 

### 代码覆盖率 ###

执行  make test 之后会在 travis config 中配置上传到[https://codecov.io](https://codecov.io), 其实就是执行的[这个脚本](https://codecov.io/bash)   
[参照](https://github.com/codecov/example-go)就可以看到[详细数据](https://codecov.io/gh/gin-gonic/gin)了    


### docs ###

[go doc](https://godoc.org/github.com/gin-gonic/gin) will auto generate    




### 参考资料 ###

[https://www.paperplanes.de/2013/10/18/the-smallest-distributed-system.html](https://www.paperplanes.de/2013/10/18/the-smallest-distributed-system.html)
