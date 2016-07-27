---
layout: wp
title: lua study notes
--- 

1. compile source code:  
yum install -y readline-devel  
curl -R -O http://www.lua.org/ftp/lua-5.3.3.tar.gz  
tar zxf lua-5.3.3.tar.gz  
cd lua-5.3.3  
make linux test  

2) 源码结构  


3) 源码阅读  

lua.h: 

定义常量：
eg:  

```
#define LUA_VERSION_MAJOR "5"
...
#define LUA_VERSION "lua" LUA_VERSION_MAJOR "." LUA_VERSION_MINOR 
...
#define LUA_OK      0
#define LUA_YIELD   1

```

定义函数指针  
```
typedef int (*lua_Writer) (lua_State *L, const void *p, size_t sz, void *ud);
```


