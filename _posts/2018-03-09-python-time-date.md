---
layout: wp
title: python time and date
comments: true
---

### convert str to timestamp ###

如果在 PHP 中，直接使用:   

`strtotime("2018-01-01")`  

但在`python`中，这似乎成了一件头疼的事情。   

python 的`time` 是什么的？ 

`time` 这个 module 提供了函数管理时间值。有两种方式表示:   

 * seconds since the Epoch,in UTC   
 * tuple of 9 integers (year, month, day, hours, minutes, seconds, weekday, Julian day, DST flag)     

我们能用它来做什么呢？  

time.time() --- 返回当前时间戳,可恨的是，它不支持参数，只能返回当前的，如果想要返回其他时间段的不可以   

time.localtime() --- 返回当前的时间time.struct_time类型  

两种表示怎么转换呢？   


#### str to timestamp ###

**strptime->time.mktime**   

time.mktime(time.localtime())

你如果需要将'2018-01-01'转换成时间戳,需要先用 tuple 表示:   
```
time.strptime("2018-01-01", "%Y-%m-%d")
```

然后转换成时间：   

```
time.mktime(time.strptime("2018-01-01"))
```

### timestamp to str ###

先将时间戳转化成struct time:   

```
time.localtime(1520578947.458189)
```

再转成 string **time.localtime->time.strftime**:   

```
# 格式化
time.strftime("%Y-%m-%d %H:%M:%S +0000", time.localtime(1520578947.458189))
# 直接返回 string
time.asctime(time.localtime(1520578947.45889))
```

可以看到，没有一个直接的 str2time 之类的方法，而是要有一个中间的 `struct_time` 阶段，WHY？   

### datetime ###

* date(year, month, day) --> date object   
* datetime(year, month, day[, hour[, minute[, second[, microsecond[,tzinfo]]]]])   
* tzinfo: bstract base class for time zone info objects   
* time([hour[, minute[, second[, microsecond[, tzinfo]]]]]) --> a time object  
* timedelta: Difference between two datetime values  
 
#### 时区转换 ####

```
from dateutil import tz
src = datetime.datetime(2018,03,11)
from_zone = tz.gettz('UTC')  
src_inzone = src.replace(tzinfo=from_zone)
to_zone = tz.gettz('CST')
src_inzone.astimezone(tz=to_zone)
```

#### timestamp  string datetime ####

```
# datetime to string
a = datetime.datetime(2018,01,01)
a.strftime('%Y-%m-%d')

# string to datetime
datetime.datetime.strptime('20180101','%Y%m%d')

# timestamp to datetime
datetime.datetime.fromtimestamp(1520746754)
# datetime to timestamp
a.strftime('%s')


# datetime to time tuple
a.timetuple()
# current datetime
datetime.datetime.today(), datetime.datetime.now()
```
