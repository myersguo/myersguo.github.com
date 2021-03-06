---
layout: wp
title: python function arguments are passed using call by value
---

Q;以下`python`代码将输出什么?  


```python
def f(a=1, L=[]):
    L.append(a)
    a +=1 
    return L
a = 1 
print f(a)
print f(a)
print f(a)
```


A:   


```python
(2, [1])
(2, [1, 1])
(2, [1, 1, 1])
```


分析：   

在`python`中一切都是对象。   



```
dir(f)
f.func_code
```

既然一切都是对象，python[对象的分配](https://docs.python.org/2/c-api/memory.html)应该都在`private heap`中。而不存在于`stack`中。python中的`a = 1`，其实是将 a 指向 1 这个整数对象,如果 `a=2`，a 的地址将发生变化。   



```
a = 1
print id(a)
a = 2 
print id(a)
b = 1
print id(b)

```

可以看到 a,b 先后指向同一个地址，后 a 指向 2 这个对象所在地址。   


回到我们的问题中的例子，我们打印出地址：   


```python
def f(a=1, L=[]):
    print 'before a address', hex(id(a))
    print 'before L address', hex(id(L))
    L.append(a)
    a +=1 
    print 'after a address', hex(id(a))
    print 'after L address', hex(id(L))                                                                         
    return a,L 
```

可以发现,** a 的地址在 a 相等的时候是不变的**，**而 L 的地址一直是不变的**。因为 `list` 这种 `mutable` 对象( list, dict, set, user-defined class, bytearray) 中包含地址的引用，可以更改地址，因此在参数中传递后会将引用传递过去，内容发生了变化。(注，python 中的默认参数只初始化一次)      



### 扩展 ###


看[PythonObject](https://github.com/python/cpython/blob/master/Include/object.h)、[list的实现代码](https://github.com/python/cpython/blob/master/Objects/listobject.c), 可以看到python的`object`都是基于双向链表的实现。他们的具体操作的实现可以继续阅读源码,这里不做展开。     



### 参考资料 ###


[http://www.cs.cornell.edu/courses/cs1110/2015fa/lectures/09-24-15/presentation-10.pdf](http://www.cs.cornell.edu/courses/cs1110/2015fa/lectures/09-24-15/presentation-10.pdf)  
[https://docs.python.org/2/c-api/memory.html](https://docs.python.org/2/c-api/memory.html)  
[http://akaptur.com/blog/2013/11/17/introduction-to-the-python-interpreter-3/](http://akaptur.com/blog/2013/11/17/introduction-to-the-python-interpreter-3/)   
[https://medium.com/@meghamohan/mutable-and-immutable-side-of-python-c2145cf72747](https://medium.com/@meghamohan/mutable-and-immutable-side-of-python-c2145cf72747)   


