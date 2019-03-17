---
layout: wp
title: Python Multiple Thread
comments: true
---

关键词：锁(lock), 条件(condition), 信号量(Semaphore),
事件(event), 队列(queue), 共享内存(shared object)

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

上面说 python 没有自己的线程池有误。实际上 可以使用 multiprocessing 中的线程池   

```
import time
from multiprocessing.dummy import Pool as ThreadPool
#from multiprocessing.pool import ThreadPool


def f(n):
  print n
  time.sleep(1)

if __name__ == '__main__':
  pool = ThreadPool(10)
  pool.map(f, range(5)
  pool.close()
  pool.join()
```

那多进程里的这个线程池的目的是什么呢？    
当任务是 io密集型(网络请求，文件处理)的建议选择多线程模式；当为cpu密集型（计数、循环）选择多进程模型。


### 多进程 ###

python 使用 multipleprocess 模块表示多进程：   

```
from multiprocessing import Process
import os

def info(title):
    print title
    print 'module name:', __name__
    if hasattr(os, 'getppid'):  # only available on Unix
        print 'parent process:', os.getppid()
    print 'process id:', os.getpid()

def f(name):
    info('function f')
    print 'hello', name

if __name__ == '__main__':
    info('main line')
    p = Process(target=f, args=('bob',))
    p.start()
    p.join()
```

多进程直接数据通信，可以使用 Queue or Pipes:   

```
from multiprocessing import Process, Queue

def f(q):
    q.put([42, None, 'hello'])

if __name__ == '__main__':
    q = Queue()
    p = Process(target=f, args=(q,))
    p.start()
    print q.get()    # prints "[42, None, 'hello']"
    p.join()

```

使用共享内存(shared object)进行数据共享：   

```
from multiprocessing import Process, Value, Array

def f(n, a):
    n.value = 3.1415927
    for i in range(len(a)):
        a[i] = -a[i]

if __name__ == '__main__':
    num = Value('d', 0.0)
    arr = Array('i', range(10))

    p = Process(target=f, args=(num, arr))
    p.start()
    p.join()

    print num.value
    print arr[:]
```


使用  lock 进行同步:   

```
from multiprocessing import Process, Lock

def f(l, i):
    l.acquire()
    print 'hello world', i
    l.release()

if __name__ == '__main__':
    lock = Lock()

    for num in range(10):
        Process(target=f, args=(lock, num)).start()
```

原生的进程池支持   

```
from multiprocessing import Pool, TimeoutError
import time
import os

def f(x):
    return x*x

if __name__ == '__main__':
    pool = Pool(processes=4)              # start 4 worker processes

    # print "[0, 1, 4,..., 81]"
    print pool.map(f, range(10))

    # print same numbers in arbitrary order
    for i in pool.imap_unordered(f, range(10)):
        print i

    # evaluate "f(20)" asynchronously
    res = pool.apply_async(f, (20,))      # runs in *only* one process
    print res.get(timeout=1)              # prints "400"

    # evaluate "os.getpid()" asynchronously
    res = pool.apply_async(os.getpid, ()) # runs in *only* one process
    print res.get(timeout=1)              # prints the PID of that process

    # launching multiple evaluations asynchronously *may* use more processes
    multiple_results = [pool.apply_async(os.getpid, ()) for i in range(4)]
    print [res.get(timeout=1) for res in multiple_results]

    # make a single worker sleep for 10 secs
    res = pool.apply_async(time.sleep, (10,))
    try:
        print res.get(timeout=1)
    except TimeoutError:
        print "We lacked patience and got a multiprocessing.TimeoutError"
```



[http://chriskiehl.com/article/parallelism-in-one-line/](http://chriskiehl.com/article/parallelism-in-one-line/)   
[https://segmentfault.com/a/1190000007495352](https://segmentfault.com/a/1190000007495352)   
[https://stackoverflow.com/questions/46045956/whats-the-difference-between-threadpool-vs-pool-in-python-multiprocessing-modul](https://stackoverflow.com/questions/46045956/whats-the-difference-between-threadpool-vs-pool-in-python-multiprocessing-modul)   


