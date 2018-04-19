---
layout: wp
title: Gin, Golang HTTP web framework
comments : true
---

近期几个 api server 使用 [gin](https://github.com/gin-gonic/gin) 框架来实现。这里记录下 gin 的基本使用。   
来看 gin 的`hello,world`:  

```
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/ping", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "pong",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080
}
```

ok, 如果我们使用原生的 [http 服务](https://golang.org/doc/articles/wiki/)怎么写呢？   

```
package main

import (
	"net/http"
)

func main() {
	http.HandleFunc("/ping", func(w http.ResponseWriter, r *http.Request) {
		w.Header().Set("Content-Type", "application/json")
		w.Write([]byte("{\"message\":\"pong\"}"))
	})

	http.ListenAndServe(":9095", nil)
}
```

看上去差不太多？再看 gin 提供的强大能力，把官方例子下载到本地：   
`curl https://raw.githubusercontent.com/gin-gonic/gin/master/examples/basic/main.go > main.go`   

```
package main

import (
	"github.com/gin-gonic/gin"
)

var DB = make(map[string]string)

func setupRouter() *gin.Engine {
	// Disable Console Color
	// gin.DisableConsoleColor()
	r := gin.Default()

	// Get 请求
	r.GET("/ping", func(c *gin.Context) {
		c.String(200, "pong") // 返回字符串
	})

	// Get user value
	r.GET("/user/:name", func(c *gin.Context) {
		user := c.Params.ByName("name") // 获取参数
		value, ok := DB[user]
		if ok {
			c.JSON(200, gin.H{"user": user, "value": value}) // 返回 JSON 数据
		} else {
			c.JSON(200, gin.H{"user": user, "status": "no value"})
		}
	})

	// Authorized group (uses gin.BasicAuth() middleware)
	// Same than:
	// authorized := r.Group("/")
	// authorized.Use(gin.BasicAuth(gin.Credentials{
	//	  "foo":  "bar",
	//	  "manu": "123",
	//}))
	authorized := r.Group("/", gin.BasicAuth(gin.Accounts{
		"foo":  "bar", // user:foo password:bar
		"manu": "123", // user:manu password:123
	})) // 认证检查

	authorized.POST("admin", func(c *gin.Context) {
		user := c.MustGet(gin.AuthUserKey).(string)

		// Parse JSON
		var json struct {
			Value string `json:"value" binding:"required"`
		}

		if c.Bind(&json) == nil {
			DB[user] = json.Value
			c.JSON(200, gin.H{"status": "ok"})
		}
	})

	return r
}

func main() {
	r := setupRouter()
	// Listen and Server in 0.0.0.0:8080
	r.Run(":9095")
}
```

### 初探 ###

看上面的例子，你几乎只需要定义好 path, handlefunc 即可：  

```
type HandlerFunc func(*Context)
```

gin 实现了 servehttp 的方法：   

```
// Conforms to the http.Handler interface.
func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
	c := engine.pool.Get().(*Context)
	c.writermem.reset(w)
	c.Request = req
	c.reset()

	engine.handleHTTPRequest(c)

	engine.pool.Put(c)
}
```

详细的 path -> handle 的逻辑这里不详细展开.   


### 参数获取 ###

获取 path 中变量：   

```
router.GET("/user/:name", func(c *gin.Context) {
		name := c.Param("name")
		c.String(http.StatusOK, "Hello %s", name)
	})
```

获取URL中参数：   

```
	// The request responds to a url matching:  /welcome?firstname=Jane&lastname=Doe
router.GET("/welcome", func(c *gin.Context) {
		firstname := c.DefaultQuery("firstname", "Guest")
		lastname := c.Query("lastname") // shortcut for c.Request.URL.Query().Get("lastname")
		c.String(http.StatusOK, "Hello %s %s", firstname, lastname)
	})
```

获取 POST 请求参数:   

```
//POST /post?id=1234&page=1 HTTP/1.1
//Content-Type: application/x-www-form-urlencoded
//
//name=manu&message=this_is_great

router.POST("/form_post", func(c *gin.Context) {
		message := c.PostForm("message")
		nick := c.DefaultPostForm("nick", "anonymous")
		c.JSON(200, gin.H{
			"status":  "posted",
			"message": message,
			"nick":    nick,
		})
	})
```

获取上传文件:  

```
file, _ := c.FormFile("file")
//or
form, _ := c.MultipartForm()
		files := form.File["upload[]"]
```

### 返回 ###

HTML :   

```
router.LoadHTMLGlob("templates/*")
c.HTML(http.StatusOK, "index.tmpl", gin.H{
	"title": "Main website",
})
```


JSON 格式:   

```
c.JSON(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
var msg struct {
	Name    string `json:"user"`
	Message string
	Number  int
}
msg.Name = "Lena"
msg.Message = "hey"
msg.Number = 123
// Note that msg.Name becomes "user" in the JSON
// Will output  :   {"user": "Lena", "Message": "hey", "Number": 123}
c.JSON(http.StatusOK, msg)
```

XML:   

```
c.XML(http.StatusOK, gin.H{"message": "hey", "status": http.StatusOK})
```

静态文件：  

```
router.Static("/assets", "./assets")

```

### 状态码 ###

重定向：  

```
c.Redirect(http.StatusMovedPermanently, "http://www.google.com/")

```

### 使用中间件 ###

```
func (engine *Engine) Use(middleware ...HandlerFunc) IRoutes {
	engine.RouterGroup.Use(middleware...)
	engine.rebuild404Handlers()
	engine.rebuild405Handlers()
	return engine
}
```

```
r.Use(gin.Recovery())
```

### 注意 ###

>When starting new Goroutines inside a middleware or handler, you SHOULD NOT use the original context inside it, you have to use a read-only copy.

```
r.GET("/long_async", func(c *gin.Context) {
		// create copy to be used inside the goroutine
		cCp := c.Copy()
		go func() {
			// simulate a long task with time.Sleep(). 5 seconds
			time.Sleep(5 * time.Second)

			// note that you are using the copied context "cCp", IMPORTANT
			log.Println("Done! in path " + cCp.Request.URL.Path)
		}()
	})
```

### 多服务 ###

```
package main

import (
	"log"
	"net/http"
	"time"

	"github.com/gin-gonic/gin"
	"golang.org/x/sync/errgroup"
)

var (
	g errgroup.Group
)

func router01() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 01",
			},
		)
	})

	return e
}

func router02() http.Handler {
	e := gin.New()
	e.Use(gin.Recovery())
	e.GET("/", func(c *gin.Context) {
		c.JSON(
			http.StatusOK,
			gin.H{
				"code":  http.StatusOK,
				"error": "Welcome server 02",
			},
		)
	})

	return e
}

func main() {
	server01 := &http.Server{
		Addr:         ":8080",
		Handler:      router01(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}

	server02 := &http.Server{
		Addr:         ":8081",
		Handler:      router02(),
		ReadTimeout:  5 * time.Second,
		WriteTimeout: 10 * time.Second,
	}

	g.Go(func() error {
		return server01.ListenAndServe()
	})

	g.Go(func() error {
		return server02.ListenAndServe()
	})

	if err := g.Wait(); err != nil {
		log.Fatal(err)
	}
}
```


