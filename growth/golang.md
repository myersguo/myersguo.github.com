---
layout: wp
title: golang notes
comments: true
---

### basic ###

### defer ###

### context ###


### sync ###

sync 提供并发的同步方案。常见的有 Mutex, RWMutex, WaitGroup, Once. Cond。 

#### Cond ####

```
type Cond struct {
    L Locker
}
```

```
func process_signal() {
    var m = new(sync.Mutex)
    var c = sync.NewCond(m)

    go func() {
        m.Lock()
        defer c.L.Unlock()
        fmt.Println("lock and wait")
        c.Wait()
        fmt.Println("unlock")
        time.Sleep(time.Second * 1)
    }()
    time.Sleep(time.Second * 1)
    fmt.Println("signal")
    c.Signal()
    time.Sleep(time.Second * 1)
}
```

Wait 会 unlock c.L 然后挂起当前协程, 重新执行时 lock c.L 。wait 被 `broadcast ` or `signal` 唤醒。   
Broadcast 想所有 wait 的 condition 发送信号。   

#### Pool #### 

> Pool's purpose is to cache allocated but unused items for later reuse, relieving pressure on the garbage collector. That is , it makes it easy to build efficient, thread-safe free lists. However, it is not suitable for all free lists.   

```
package main
import (
	"fmt"
	"sync"
	"runtime"
)

func process() {
	pool := sync.Pool{
		New: func() interface {} {
			return nil
		},
	}
	pool.Put(1)
	pool.Put(3)
	runtime.GC() // gc will remove all pool value
	fmt.Println(pool.Get())
	pool.Put(3)
	fmt.Println(pool.Get())
}

func main() {
	process()
}
```


### middleware ###

```
package main

import (
	"fmt"
	"net/http"
)

func Middleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		ctx := r.Context()
		fmt.Println("run middleware") // do what you want
		next.ServeHTTP(w, r.WithContext(ctx))
	})
}

type MyHandler struct {
}

func (h *MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Hello, world!\n"))
}

func main() {
	http.Handle("/", &MyHandler{})
	http.Handle("/route", Middleware(&MyHandler{}))
	http.Handle("/route/next", Middleware(Middleware(&MyHandler{})))
	http.ListenAndServe(":8000", nil)
}
```


### tips ###

* 进程间的通信方式: pipe,eg: dmesg | less; 共享内存(shm); 消息通信;
* 线程的同步方式： mutex,  condition, sem。    


