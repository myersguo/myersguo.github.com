---
layout: wp
title: write python first
---

## 安装虚拟环境 ##

>
>[虚拟环境](http://docs.python-guide.org/en/latest/dev/virtualenvs/)解决不同的项目依赖版本不同的问题。保持本地全局包的干净。  
>它创建独立的(isolated)python环境。


```
sudo yum install python-devel
sudo pip install virtualenv
cd my_project
virtualenv -v -p /home/work/app/python27/bin/python env
touch requirements.txt
#激活当前项目虚拟环境
source env/bin/activate
```

可以获取本地环境的依赖：  

`pip freeze > requirements.txt`  


## package & module ##

[module search path](https://docs.python.org/2/tutorial/modules.html)  

python的module查找是从`sys.path`中，sys.path来自：   


* 输入脚本的当前目录。   
* PYTHONPATH,一系列目录名，在python安装的时候就已经确定。   
* 安装时默认值   

如果想要让python找到不在sys.path中的模块，就需要添更改sys.path:   


```
import os, sys
sys.path.append(os.path.dirname(os.path.abspath(__file__))+"/../../")
```

想要追看python的执行过程：  

```
python -m trace -c -t xxx.py
```


一个简单的测试：  

```
test
--modulea
----a.py
--moduleb
----b.py
test.py
```

其中`a.py`导入b.py：　　　

```
from module b import b
```

执行`python a.py`会报错:   

```
ImportError: No module named xxx
```

因为执行时sys.path找不到moduleb，那怎么才不报错：   
test.py作为入口，导入a,执行就不会报错了．  

```
#test.py
from module import a

```



## in list or tuple ##


比如判断是否为windows环境：   

```
def is_windows():
    if os.name in ('nt','ce'):
        return True
    return False
```

这里 os.name in `('nt','ce')` 能否更改为 ` os.name in ['nt','ce']` 呢？   

当然可以，但那个速度够快呢，我们做个测试：  

```
import time
import random

a = []
for x in range(9999999):
    a.append(x)


b = tuple(a)

suma = 0
sumb = 0
y = 0
while y<10:
    r = random.randrange(1,9999999)
    start = time.time()
    check = r in a
    end = time.time()
    diff = end-start
    suma += diff
    start2 = time.time()
    check2 = r in b
    end2 = time.time()
    diff2 = end2-start2
    sumb += diff2
    print 'list(time: %s) is %s than tuple time(%s)' % (diff, 'fast' if diff2>diff else 'slow',diff2)
    y +=1

print 'list(time: %s) is %s than tuple time(%s)' % (suma, 'fast' if sumb>suma else 'slow',sumb)


```

测试结果为：  

```
list(time: 0.297999858856) is slow than tuple time(0.296000003815)
list(time: 0.0820000171661) is slow than tuple time(0.0769999027252)
list(time: 0.503000020981) is slow than tuple time(0.496999979019)
list(time: 0.113000154495) is fast than tuple time(0.116999864578)
list(time: 0.261000156403) is fast than tuple time(0.286999940872)
list(time: 0.00500011444092) is slow than tuple time(0.000999927520752)
list(time: 0.131999969482) is slow than tuple time(0.128000020981)
list(time: 0.197000026703) is slow than tuple time(0.186000108719)
list(time: 0.0549998283386) is slow than tuple time(0.0510001182556)
list(time: 0.531000137329) is fast than tuple time(0.531999826431)

list(time: 2.17700028419) is slow than tuple time(2.17199969292)
```
再多测试几次挥发现，出现`list(time: 5.91100049019) is fast than tuple time(6.1219997406)`,因此可以判断，list和tuple在查找上(in)速度差不多((/ □ \))   

如果固定不变，就用tuple，不需要考虑性能问题。   




### 参考资料 ###

[http://docs.python-guide.org/en/latest/dev/virtualenvs/](http://docs.python-guide.org/en/latest/dev/virtualenvs/)  
[http://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html](http://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html)  


