---
layout: wp
title: what is thread local
---

进程之间是不共享内存的。不存在数据争强的问题，但是线程的数据是同内存种的，多线程模式下，对同一变量进行修改，可能造成数据不一致的情况。有两种解决办法：使用局部变量或者thread local variable。  
使用局部变量有一个问题是在多个函数共同使用要通过参数传递方式实现。   
 
线程争强例子：   


```
import threading

total_cnt = 0

def process_1():
    global total_cnt
    for x in xrange(500):
        total_cnt += 1

if __name__ == "__main__":
    for x in xrange(2):
        t = threading.Thread(target=process_1)
        t.start()
    print total_cnt
```

执行100次上面的代码:   
`for((x=0;x<100;x+=1));do python thread_local.py;done` 你会发现结果不是全部都是 1000。 因为多线程下global variable 不是原子操作。对同一个数据操作需要加锁。   

```
l = threading.Lock()
def process_1():
    global total_cnt
    if l.acquire():
        try:
            for x in xrange(500):
                total_cnt += 1
        finally:
            l.release()
```

local thread 一般用于解决线程之间数据隔离的问题。假如有一个 global 的数据，如果只在当前线程下有效，就可以使用 threading.local。 它是怎么实现的呢？可以参考[werkzeug](https://github.com/pallets/werkzeug/blob/master/src/werkzeug/local.py)的实现。它使用`__storage__`来保存变量，每个线程使用不同的dict 的 id (通过 get_ident) 区分, 从而保证了线程的安全。    
   



