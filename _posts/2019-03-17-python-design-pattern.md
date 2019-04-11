---
layout: wp
title: design pattern
---

### Singleton Pattern 单例模式 ###

import once, so singleton as following:  

```
class Singleton(object):
    def test():
        pass
singleton_service = Singleton()
#我们最常用的方式
```

or:   

```
class Singleton(object):
    _instance = None
    def __new__(class_, *args, **kwargs):
        if not isinstance(class_._instance, class_):
            class_ = super(Singleton, class_).__new__(class_, *args, **kwargs)
        return class_._instance
```

or metaclass

```
class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]

class Logger(object):
    __metaclass__ = Singleto
```

### borg pattern ###

将`__dict__` 绑定到全局对象上。 造成单例的功能。   
```
class Borg(object):
    __shared_state = {}

    def __init__(self):
        self.__dict__ = self.__shared_state
        self.state = 'Init'

    def __str__(self):
        return self.state
```


### adapter 适配器模式  ###


>the adapter pattern is a software design pattern (also known as wrapper, an alternative naming shared with the decorator pattern) that allows the interface of an existing class to be used as another interface. It is often used to make existing classes work with others without modifying their source code.


```
import functools
class AdapterClient(object):
    def __init__(self, **kwargs):
        pass
    def __getattr__(self, item):
        def wrapper(self, *args, **kwargs):
            print "run:", item
        return functools.partial(wrapper, self)

class Adapter(object):
    def __init__(self, **kwargs):
        self.client = AdapterClient()
    def __getattr__(self, item):
        return getattr(self.client, item)

adpt = Adapter(a=1)
adpt.add()
adpt.minus()
```
    
### factory pattern 工厂模式  ###

In class-based programming, the factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.   


创建不同的对象。


```
class AFactory(object):
    def get_obj(self, Obj):
        return Obj()

class Cat():
    def test(self):
        print "cat"

class Dog():
    def test(self):
        print "dog"

a = AFactory()
cat = a.get_obj(Cat)
cat.test()

dog = a.get_obj(Dog)
dog.test()
```


### 参考  ###
[https://github.com/faif/python-patterns](https://github.com/faif/python-patterns)
