---
layout: wp
title: Python 2.7 documentation notes
---

杂乱记录，仅供自己参阅。   



### 关键字 ###

nested function   
list comprehensions   
`key: value pairs`   
sequence   
[]、{}、()   
module.func   




### 疑问 ###

why use tuple ?   


### string ###


'{0}' .format('string')   
'{food} is {adjective}' .format(food='span', adjective='absolutely horrible')   
'{0:.3f}'.format(3.1415926)
'{0[name]:10s}' .format( {"name":"myersguo"} )  
[more](https://docs.python.org/2.7/library/string.html#formatstrings)   





### not, and , or  ###


```
not []
not ()
not False
not ''
not 0
```


and   


```
'And' and 'yes'
True and ''
True and 'yes'
```


### compare ###


```
>>> (1, 2, 3) < (1, 2, 4)
True
>>> (1, 2, 3) < (1, 3, 2)
True
>>> [1, 2, 3] < [5,2]    
True
```


### input or output ###

f.tell()  
f.seek()   




### 参考资料 ###


[python tutorial](https://docs.python.org/2.7/tutorial/)    
[python library](https://docs.python.org/2.7/library/index.html)   
[python referenc](https://docs.python.org/2.7/reference/)  
[python object](https://github.com/python/cpython/blob/master/Include/object.h#L346)  
[python list object](https://github.com/python/cpython/blob/master/Include/listobject.h)   
[python list object.c](https://github.com/python/cpython/blob/master/Objects/listobject.c)  
[https://mail.python.org/pipermail/tutor/2001-September/008888.html](https://mail.python.org/pipermail/tutor/2001-September/008888.html)   


