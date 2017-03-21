---
layout: wp
title: python function arguments are passed using call by value
---

Q;以下`python`代码将输出什么?  


```python
def f(a, L=[]):
    L.append(a)
    a +=1 
    return L

a = 1 
L = []
print f(a)
print f(a)
print f(a)
print a
print L 
```


A:   


```python
[1]
[1, 1]
[1, 1, 1]
1
[]
```










