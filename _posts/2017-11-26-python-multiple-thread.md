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

``
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


