---
layout: wp
title: Write To Write Go Code
---

本文基本来自最后的[参考](#reference).   

==  组织结构 
所有的go代码都必须在一个工作区中。(eg: mkdir $HOME/www/mycode)
工作区中包含很多版本控制的代码仓库repository，每个repository都存在于单独的文件夹下（package）;
每个package包含一个或多个source code;  
package的路径即要import的路径。（eg: import "cron",需要在src/cron这个package下找）   

工作区包含以下文件夹：   
src:GOLANG的源代码    
pkg:package object    
bin:可执行文件  

go tools编译src文件夹(source packages)，install生成的二进制文件到pkg和bin目录下。   

workspace项目例子：  


```
bin/
    hello                          # command executable
    outyet                         # command executable
pkg/
    linux_amd64/
        github.com/golang/example/
            stringutil.a           # package object
src/
    github.com/golang/example/
        .git/                      # Git repository metadata
        hello/
            hello.go               # command source
        outyet/
            main.go                # command source
            main_test.go           # test source
        stringutil/
            reverse.go             # package source
            reverse_test.go        # test source
    golang.org/x/image/
        .git/                      # Git repository metadata
        bmp/
            reader.go              # package source
            writer.go              # package source
        ... (many more repositories and packages omitted) ...

````

== GOPATH  







<span id="reference">参考:</span>     

1.[How to Write Go Code](https://golang.org/doc/code.html)  
2.[GO SPEC](https://golang.org/ref/spec)  
3.[Effective Golang](https://golang.org/doc/effective_go.html)  
4.[A tour of Go](https://tour.golang.org/welcome/1)  



