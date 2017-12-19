---
layout: wp
title: golang json 处理
comments: true
---

### 用法 ###

【TODO】

### 疑问 ###

golang json 解析后竟然是按照`key`字母升序的排列：   

```
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	Name string	`json:"name"`
	Id   int	`json:"id"`
}

func getJson(v interface{}) (string, error) {
	s, err := json.Marshal(v)
	fmt.Println("before:", string(s))
	if err != nil {
		return "", err
	}
	m := make(map[string]interface{})
	json.Unmarshal(s, &m)
	r, _ := json.Marshal(m)
	return string(r), nil
}

func main() {
	var t1 = User{
		Name: "hello",
		Id:   1,
	}
	v, _ := getJson(&t1)
	fmt.Println("after:", v)
}
```

输出：   
`before: {"name":"hello","id":1}  
after: {"id":1,"name":"hello"}`   

这样如果有其他语言要和`golang`保持一致（比如做sign）,那就要做相应的处理，如 python:   

```
json.dumps(v, sort_keys=True, separators=(',', ':'))
```


