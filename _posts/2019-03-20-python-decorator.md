---
layout: wp
title: decorator
---

```
def my_decorator(f):
    def wrapper():
        print "before"
        f()
        print "after"
    return wrapper

def test():
    print "test"

my_test = my_decorator(test)
my_test()
print test.__name__
print my_test.__name__
```

每个函数作为「注解」的参数，注解的调用返回一个 wrapper 函数。
这个时候，my_test 变成了 `wrapper`


### @语法糖 syntactic sugar ###

```
@my_decorator
def test2():
    print "test2"

#test2 = my_decorator(test2)

test2()
```

函数参数：   

```
def my_decorator(f):
    def wrapper(*args, **kwargs):
        print "before"
        f(*args, **kwargs)
        print "after"
    return wrapper

def test(name):
    print "test", name

@my_decorator
def test2(name):
    print "test2", name

my_test = my_decorator(test)
my_test("xiaoming")
test2("xiaoming2")
```

注解带参数：    

```
def with_params_decorator(n=0):
    def my_decorator(f):
        @functools.wraps(f)
        def wrapper(*args, **kwargs):
            print n
            print "before"
            f(*args, **kwargs)
            print "after"
        return wrapper
    return my_decorator
```

多套了一层。。。 


为了解决, python 类型自省变动问题，使用 `@functools.wrapper`来解决。来看下这个注解：  

```

WRAPPER_ASSIGNMENTS = ('__module__', '__name__', '__doc__')
WRAPPER_UPDATES = ('__dict__',)

def update_wrapper(wrapper,
                   wrapped,
                   assigned = WRAPPER_ASSIGNMENTS,
                   updated = WRAPPER_UPDATES):
    """Update a wrapper function to look like the wrapped function

       wrapper is the function to be updated
       wrapped is the original function
       assigned is a tuple naming the attributes assigned directly
       from the wrapped function to the wrapper function (defaults to
       functools.WRAPPER_ASSIGNMENTS)
       updated is a tuple naming the attributes of the wrapper that
       are updated with the corresponding attribute from the wrapped
       function (defaults to functools.WRAPPER_UPDATES)
    """
    for attr in assigned:
        setattr(wrapper, attr, getattr(wrapped, attr))
    for attr in updated:
        getattr(wrapper, attr).update(getattr(wrapped, attr, {}))
    # Return the wrapper so this can be used as a decorator via partial()
    return wrapper

def wraps(wrapped,
          assigned = WRAPPER_ASSIGNMENTS,
          updated = WRAPPER_UPDATES):
    """Decorator factory to apply update_wrapper() to a wrapper function

       Returns a decorator that invokes update_wrapper() with the decorated
       function as the wrapper argument and the arguments to wraps() as the
       remaining arguments. Default arguments are as for update_wrapper().
       This is a convenience function to simplify applying partial() to
       update_wrapper().
    """
    return partial(update_wrapper, wrapped=wrapped,
                   assigned=assigned, updated=updated)
```

wraps 这个返回的是 partial 更新了的 `update_wrapper`,默认值 wrapped=wrapperd,即:   

```
def wraps(wrapped):
	def new_wraps():
		return update_wrapper(wrapped=wrappwd)
	return new_wraps
```

被 functions.wraps 注解的函数，的 name, doc 等都不会变。    


### 类注解 ###

```
class class_decorator(object):
    def __init__(self, f):
		functools.update_wrapper(self, func)
        self.f = f
    def __call__(self, *args, **kwargs):
        print "before"
        self.f(*args, **kwargs)
        print "after"

my_test = class_decorator(test)
my_test("class")
```

类注解满足两点：1) __init__ 传入 func, 2) __call__ 可执行   


