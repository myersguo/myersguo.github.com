---
layout: wp
title: 压力测试工具 vegeta
---

介绍一下用`golang`写的压力测试工具[vegeta](https://github.com/tsenart/vegeta)（发音：喂，吉他），还是很强大的。   

如果不需要作为lib库使用，直接下载二进制文件即可.[点击下载](https://github.com/tsenart/vegeta/releases)     

### 直接使用 ###


```
echo "GET https://github.com" |./vegeta attack -duration=10s -rate=100 >results.bin
```



###查看结果###

   

```
./vegeta report -inputs=results.bin -reporter=json 
```   

或者：   

```
./vegeta report -inputs=results.bin 
```

生成测试报告:   

```
./vegeta report -inputs=results.bin --reporter=plot  > plot.html
```   


### 增加HTTP头 ###



假如有需要登录的操作，可提前在web上登录，然后拿到cookie信息,vegeta命令执行时加入cookie即可。如：    


```
echo "GET http://github.com/" | ./vegeta attack -header="Cookie:xxx=QkJEMURGOEUzMkEwRkM1QUI1OUExQ0VFRjk2MDY3QjkwNTBDRjhDNjRCNkJBQzUwMDNEMEJCMDczNTg0MUM5RDU3RTRERUVDMEFBMUI0Rjc4QkE0RkFCQTg1MkNFMkZFMzIwN0I1QjFFQjlGRjk0MzkxOTRBRDE2RUJGRTAzQTQxRTg1NkU3RkQ4MEQ2MDhCNTI3RUM4QUExNTc2MDMxOQ%3D%3D; ddd=myersguo; PHPSESSID=xxx"  -duration=3s -rate=10 > results.bin`
```




### POST ###




post的参数都是放到文件中的,提前写到文件中:   


```
echo "POST http://localhost" |./vegeta attack -body /tmp/body.txt --duration=1s -rate=10 > results.bin

```


### 多个请求 ###



`vegeta`可以读文件来发起多个请求.   

请求文件为：target.txt，如下：   

```
POST http://goku:9090/things
@/path/to/newthing.json

PATCH http://goku:9090/thing/71988591
@/path/to/thing-71988591.json

POST http://goku:9090/things
X-Account-ID: 99
@/path/to/newthing.json

GET http://user:password@goku:9090/path/to
X-Account-ID: 8675309

DELETE http://goku:9090/path/to/remove
Confirmation-Token: 90215
Authorization: Token DEADBEEF

GET http://goku:9090/path/to/dragon?item=balls
HEAD http://goku:9090/path/to/success
```



### 实现原理 ###

我这里简化下 attack 的过程：  

先写个 [server](https://github.com/myersguo/TestExamples/blob/master/goExample/server/server.go) 等待被我们攻击吧：  

```
package main

import (
    "net/http"
    "fmt"
    "time"
)

var (
    qps = 0
    now = time.Now()
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        qps++
        cur := time.Now()
        if cur.Sub(now) >= 1e9 {
            fmt.Println(qps)
            qps = 0
            now = time.Now()
        }
    })
    http.ListenAndServe(":8887", nil)
}
```

server端会对请求 '/'， 输出当前的qps    


写一下 attack [client 端](https://github.com/myersguo/TestExamples/blob/master/goExample/loadClient/load.go)：   

```
package main

import (
    "fmt"
    "time"
    "os"
    "os/signal"
    . "runtime"
    "net/http"
    "sync"
)

const (
)

var (
)

func max(a, b time.Time) time.Time {
    if a.After(b) {
        return a
    }
    return b
}

func attack(ticks <- chan time.Time, workers *sync.WaitGroup) {
    defer workers.Done()
    for t:= range ticks {
        _, err := http.Get("http://localhost:8887/")
        if err != nil {
            fmt.Printf("%v\n", err)
        }
        fmt.Println(t, " attck")
    }
}

func main() {
    var workers sync.WaitGroup
    ticks := make(chan time.Time)
    defer close(ticks)
    sig := make(chan os.Signal, 1)
    signal.Notify(sig, os.Interrupt)
    
    rate := uint64(1500)
 
    hits := uint64(50000) 
    interval :=  1e9/uint64(rate)
    began, done := time.Now(), uint64(0)
    


    for {
        fmt.Printf("now routines %d\n", NumGoroutine())
        now, next := time.Now(), began.Add(time.Duration(done*interval))
        diff := next.Sub(now)
        time.Sleep(diff)
        fmt.Printf("now:%v, next:%v, diff:%v,done:%v\n", now, next, diff, done) 
        select {
            case ticks <- max(next, now):
                fmt.Printf("%v done %d\n", now,  done)
                if done++; done >= hits {
                    return
                }
            case <- sig:
                fmt.Println("stop")
                return
            default:
                fmt.Println("go attach")
                workers.Add(1)
                go attack(ticks, &workers)
        }
    }
    
}
```
执行 ./loadClient, 我们看到服务端的 qps 稳定在 1500左右。   

现在我们思考一个问题，能不能用下面的 code 来代替呢？   

```
select {
     case <- sig:
         fmt.Println("stop")
         return
     default:
         if done++; done >= hits{
             return
         }
         fmt.Println("go attach")
         workers.Add(1)
         go attack(ticks, &workers)
         ticks <- now
}
```

(未完待续) 
