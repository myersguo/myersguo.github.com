---
layout: wp
title: Write To Write Go Code
---
2年前，我开始接触GOLANG，想要认真[学习一下](http://myersguo.github.io/2014/08/20/go-programming-study.html),结果没有坚持下去，就像是跑步，跑了一个月之后就不跑了。GOLANG的学习，因为工作需要，需要继续认真研究一下。   

本文基本来自最后的[参考](#reference).   

## 组织结构 
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

## GOPATH  

go build工具会读GOPATH变量来编译程序，因此需要将你的workspace添加到GOPATH中。  
>export GOPATH=$home/work/mycode  

import path标识一个package.系统的($GOROOT/src下面的包)包名使用短路径(eg: fmt,net/http),包名是import最后的节点名称（net/http,包名http）  

>在同一个folder下面的文件必须使用相同的包名   
>可执行二进制文件的package name必须是main   

GOLANG包含一个轻量级的test framework,测试文件以_test命名，使用go test进行测试。  

GOLANG的工具go get，可以获取远端的仓库代码，例如：go get github.com/golang/example/hello,执行命令将下载该路径的代码到GOPATH下的src/github中。  

## GO语言规范  

### 格式,注释,名称    
代码的语言风格虽然是千人千面，但GOLANG用自动化的方式使用go fmt进行代码格式化; GOLANG提供两种注释方式:  
>block comments /* */,通常用在包的说明，在大端注释或表达式内注释也很有用；  
>line comments //    

GOLANG的代码文档生成工具GODOC，读取源码。  

>包注释： package前，eg: // Package io provides basic interfaces to I/O primitives.
>语句注释：导出的变量、函数(大写开头)都应该有注释:  

GOLANG的名称和其他语言不同，命名加入了语义，这个想法真是奇特。名称的首字母大写表示可导出。

Getter: 因为GO的首字母大写表示导出，因此Getter方法，使用大写表示更好，eg: Owner,而不是GetOwner  
接口名：方法的接口名，建议方法名后加er,eg: Reader,Writter。  

分号：GO代码中不需要使用分号来进行分割（循环中需要），GO的语法解析器将按照某种规则插入分号。但括号不应该在控制语句的下一行，因为分号将会解析后插入控制语句行。将会造成某种意想不到的错误。  

IF语句：使用或括号包含if语句，尤其是在有多行表达式的时候非常有用。  

```
if err:= file.Chmod(0664); err != nil {
    return err
}
```
if可以使用一个初始化的语句，用;分割多个表达式。  





[未完待续]()



<span id="reference">参考:</span>     

1.[How to Write Go Code](https://golang.org/doc/code.html)  
2.[GO SPEC](https://golang.org/ref/spec)  
3.[Effective Golang](https://golang.org/doc/effective_go.html)  
4.[A tour of Go](https://tour.golang.org/welcome/1)  



