---
layout: wp
title: gin notes
comments: true
---

go 提供的 http 包，写一个 hello,world:   

```
package main
import (
	"net/http"
	"io"
	"log"
)
func main() {
	http.HandleFunc("/ping", func(w http.ResponseWriter, _ *http.Request) {
		io.WriteString(w, "pong")
	})
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

使用 gin:

```
package main
import (
	"github.com/gin-gonic/gin"
)
func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context){
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run("0.0.0.0:8081")
}
```

详细看一下 gin 的方法：  

```
func (engine *Engine) Run(addr ...string) (err error) {
    defer func() {debugPrintError(err)}()
    address := resolveAddress(addr)
    err = http.ListenAndServe(address, engine)
    return
}
```

这里： `engine` 就是默认的所有唯一 handler，也就是说有的请求都会由这个统一的`handler` 处理。我们知道， http.server 的 handler必须实现 `ServeHTTP` 方法. 上面原生的方法，默认的 handler 是 `DefaultServeMux` 。我们看下 gin 默认的 handler。   


```
func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    c := engine.pool.Get().(*Context)
    c.writermem.reset(w)
    c.Request = req
    c.reset()
    engine.handleHTTPRequest(c)
    engine.pool.Put(c)
}
```

看下 engine 的初始化:  

```
func Default() *Engine {
    engine := New()
    engine.Use(Logger(), Recovery())
    return engine
}
func New() *Engine {
    engine := &Engine{
        ...
    }
    engine.pool.New = func() interface {}{
        return engine.allocateContext()
    }
    return engine
}
func (engine *Engine) allocateContext() *Context {
    return &Context{engine: engine}
}
```

> Engine is the framework's instance, it contains the muxer, middleware and configuration settings.

请求处理逻辑：   

```
func (engine *Engine) handleHTTPRequest(c *Context) {
    httpMethod := c.Request.Method 
    rPath := c.Request.URL.Path
    unescape := false
    ...
    for i, tl := 0, len(t); i < tl; i++ {
        value := root.getValue(rPath, c.Params, unescape)
        if value.handlers != nil {
            c.handlers = value.handlers
            c.Params = value.params 
            c.fullPath = value.fullPath
            c.Next()
            c.writerem.WriteHeaderNow()
            return
        }
    }

}
```

路由树的构造：   

以 GET 请求为例：   


```
func (group *RouterGroup) GET(relativePath string, handlers ...HandlerFunc) IRoutes {
    return group.handle("GET", relativePath, handlers)
}

func (group *RouterGroup) handle(httpMethod, relativePath string, handlers HandlersChain) IRoutes {
    absolutePath := group.calculateAbsolutePath(relativePath)
    handlers = group.combineHandlers(handlers)
    group.engine.addRoute(httpMethod, absolutePath, handlers)
    return group.returnObj()
}

func (engine *Engine) addRoute(method, path string, handlers HandlersChain) {
    root := engine.trees.get(method)
    if root == nil {
        root = new(node)
        root.fullPath = "/"
        engine.trees = append(engine.trees, methodTree{method: method, root: root})
    }
    root.addRoute(path, handlers)
}

```


* router   
* middleware   
* context     


### router ###

```
func (group *RouterGroup) handle(httpMethod, relativePath string, handlers HandlersChain) IRoutes {
    absolutePath := group.calculateAbsolutePath(relativePath)
    handlers = group.combineHandlers(handlers)
    group.engine.addRoute(httpMethod, absolutePath, handlers)
    return group.returnObj()
}
....
    copy(mergedHandlers, group.Handlers)
    copy(mergedHandlers[len(group.Handlers):], handlers)
    engine.trees = append(engine.trees, methodTree{method: method, root: root})
...

```

上面是添加路由的过程：  

* 获取：absolutePath   
* 合并handlers: 合并 middleware 的handler 和 路由的处理方法   
* 添加到 method tree    

gin 的每个方法对应一个树, 每次添加路由获取对应方法的根节点 (`root := engine.trees.get(method)`),  methodTree 结构：   


```
type node struct {
    path      string
    indices   string
    children  []*node
    handlers  HandlersChain
    priority  uint32
    nType     nodeType
    maxParams uint8
    wildChild bool
    fullPath  string
}
type methodTree struct {
    method string
    root   *node
}

type methodTrees []methodTree
```






### middleware ###

gin 源码中适用 middleware    


```
    engine.Use(Logger(), Recovery())
```


我们可以使用自定义的 middleware:   

```
    r.Use(func(c *gin.Context){
        fmt.Println("middleware")
    })
```

我们看一下 middleware 的底层实现：   

```
func (group *RouterGroup) Use(middleware ...HandlerFunc) IRoutes {
    group.Handlers = append(group.Handlers, middleware...)
    return group.returnObj()
}
```

所以, 可以看出， use 是把处理的 handlers 填充。然后，执行的时候，按照顺序执行 handler.默认的 gin 增加了 log 和 recovery 两个 handler 。   





### router ###

### middleware ###

