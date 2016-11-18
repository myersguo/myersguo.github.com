---
layout: wp
title: API文档和MOCK工具
---


使用git工具进行开发，若使用github或gitlab，你可能使用[gitbook](https://www.gitbook.com/)来生成说明文档。   

但，当你使用api blueprint后，你就会爱不释手。   

### aglio ###

An API Blueprint renderer with theme support that outputs static HTML  

安装：   

```
npm install -g aglio
```

用法：   

```
# Default theme
aglio -i input.apib -o output.html

# Use three-column layout
aglio -i input.apib --theme-template triple -o output.html

# Built-in color scheme
aglio --theme-variables slate -i input.apib -o output.html

# Customize a built-in style
aglio --theme-style default --theme-style ./my-style.less -i input.apib -o output.html

# Custom layout template
aglio --theme-template /path/to/template.jade -i input.apib -o output.html

# Custom theme engine
aglio -t my-engine -i input.apib -o output.html

# Run a live preview server on http://localhost:3000/
aglio -i input.apib -s

# Print output to terminal (useful for piping)
aglio -i input.apib -o -

# Disable condensing navigation links
aglio --no-theme-condense -i input.apib -o output.html

# Render full-width page instead of fixed max width
aglio --theme-full-width -i input.apib -o output.html

# Set an explicit file include path and read from stdin
aglio --include-path /path/to/includes -i - -o output.html

# Output verbose error information with stack traces
aglio -i input.apib -o output.html --verbose
```

### 实战 ###

假如我们写了个markdown : 


```
### 接口列表 ###

#### 获取CRASH统计信息 ####

GET /crash/getCount?appId=$appId&startTime=$startTime&endTime=&endTime&type=$type&limit=$limit

$startTime:
开始时间：CRASH发生的时间(!非上报时间)区间开始
$endTime:
结束时间：CRASH发生的时间(!非上报时间)区间结束
$type:
错误类型：crash,anr...(一期只有crash)

Response:  

{
    "errno": 0,
    "errmsg": "ok",
    "data": [{
            "appId": string,
            "crashNum": int,
            "crashUser": int,
        }, {
            "crashNum": int,
            "crashUser": int,
    }]
}


```

生成文档：   

```
aglio -i interface.md -o interface.html
```


作为服务： 

```
aglio -i interface.md -h 0.0.0.0 -p 9999 -s
```



参考资料：   

[https://github.com/apiaryio/api-blueprint](https://github.com/apiaryio/api-blueprint)  
[http://zencode.cn/2015/11/api-blueprint/](http://zencode.cn/2015/11/api-blueprint/)  

