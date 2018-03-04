---
layout: wp
title: Python Multiple Thread
comments: true
---

### 线程的定义 ###


* start_new_thread  

```
thread.start_new_thread(f)
```

* 使用线程类：  

```
t = threading.Thread(target=f)
t.start()
t.join()
```
t.join:    

```
self.__block.acquire() # 获取条件锁
while not self.__stopped: # 如果线程未完成，继续等待
    self.__block.wait() # 释放锁,等待结束
```

线程的执行内部是走的`run`方法，run 内部调用的 target 方法。


* 继承线程类:   

```
class MyThread(threading.Thread):
    def __init__(self):
        #threading.Thread.__init__(self)
        super(MyThread, self).__init__()
    def run(self):
        print 'thead running here'  
```

### 线程的同步 ###

基础：锁    
锁的集中方式：`Condition`, `Semaphore(一种Condition)`，`Event(一种Condition)`,`Queue`


`Condition` 使用可重入锁RLock 来实现.  

```
def producer(cv):
    # cv.acquire()
    with cv:
        print 'produce' 
        cv.notifyAll() # cv.notify(), cv.release()
def consummer(cv):
    with cv: # cv.acquire()
        cv.wait() # cv.wait(), cv.release()
        print 'consume'
```

`Event`:   

```
event.set 设置事件
event.clear() 发送事件
event.wait() 等待事件触发
```


### 线程池 ###

加入我们的任务数量有1000个，那么需要定一个一个线程队列的任务来处理，队列假如50个任务同时在跑，每次任务处理完毕，添加任务到线程池（任务队列中）。   
python 官方没有 threadpool 的模块定义,我们可以定一个简单的线程池。  

```   
# 线程池的线程，从队列获取任务，执行任务
class WorkerThread(threading.Thread):
    def __init__(self, work_queue, **kwds):
        threading.Thread.__init__(self, **kwds)
        self.work_queue = work_queue
        self.setDaemon(1)
        self.start()
    def run(self):
        while True:
            try:
                target, args = self.work_queue.get(True) # 从队列中获取任务
            except Queue.Empty:
                continue
            else:
                target(*args)
            self.work_queue.task_done()

# 线程池管理, 创建线程，开始执行    
class ThreadPool:
    def __init__(num):
        self.work_queue = Queue()
        for i in range(num):
            t = WorkerThread(self.work_queue)
            t.start()
    def putRequest(self, request, *args):
        self.work_queue.put((request, args))

```


