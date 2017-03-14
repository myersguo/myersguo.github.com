---
layout: wp
title: android performance
---

### 启动时间 ###   

[系统启动时间](https://developer.android.com/topic/performance/launch-time.html),包括三个方面**冷启动(Cold start)**，热启动(Warm start),不冷不热启动(Lukewarm start)。我们所关系的一般是冷启动即app的进程开始创建的启动过程。   

![code start](/public/images/cold-launch.png)   

>1. 创建app对象.   
>2. 启动`main`主线程.   
>3. 创建main activity.  
>4. Inflating views.   
>5. Laying out the screen.   
>6. Performing the initial draw.      

启动时间过长，一般要查看application对象,activity初始化是否`over heavy`了。    



#### 测试方法 ####   

1. logcat 中查看。   

Android 4.4(API 19)后可以在logcat中查看`Displayed`启动时间.     

```
C:\Users\guoliangyong\example>adb -s e088ebb0 shell logcat | findstr "Displayed"
I/ActivityManager(  798): Displayed com.android.settings/.MiuiSettings: +710ms
I/ActivityManager(  798): Displayed com.miui.calculator/.cal.CalculatorActivity: +587ms
```

2. adb shell am start -S -W     

```
>adb -s e088ebb0 shell am start -S -W com.android.settings/.MiuiSettings
Stopping: com.android.settings
Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.android.settings/.MiuiSettings }
Status: ok
Activity: com.android.settings/.MiuiSettings
ThisTime: 844
TotalTime: 844
Complete
```

以小米商城为例:    

```
get main activity: pm dump com.mi.global.shop | findstr "MAIN"   
C:\Users\guoliangyong\example>adb -s e088ebb0 shell am start -S -W com.mi.global.shop/.activity.MainTabActivity
Stopping: com.mi.global.shop
Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.mi.global.shop/.activity.MainTabActivity }
Status: ok
Activity: com.mi.global.shop/.activity.MainTabActivity
ThisTime: 1886
TotalTime: 1886
```
启动时间是： `1886ms`    

商城BBS启动时间:`1502ms`如下：      

```
adb -s e088ebb0 shell am start -S -W com.mi.global.bbs/.ui.MainActivity
Stopping: com.mi.global.bbs
Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.mi.global.bbs/.ui.MainActivity }
Status: ok
Activity: com.mi.global.bbs/.ui.MainActivity
ThisTime: 1502
TotalTime: 1502
Complete

```    

我们再来看今日头条的,启动时间`2107ms`：   

```
adb -s e088ebb0 shell dumpsys window windows | findstr "Current"
 mCurrentFocus=Window{439bd3d8 u0 com.ss.android.article.news/com.ss.android.article.news.activity.MainActivity}

C:\Users\guoliangyong\example>adb -s e088ebb0 shell am start -S -W com.ss.android.article.news/com.ss.android.article.news.activity.MainActivity
Stopping: com.ss.android.article.news
Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] cmp=com.ss.android.article.news/.activity.MainActivity }
Status: ok
Activity: com.ss.android.article.news/.activity.MainActivity
ThisTime: 2107
TotalTime: 2107
```

其他：   
手百： `933ms`    
手淘： `1790ms`    


（跑个题，使用am start启动京东客户端：adb -s e088ebb0 shell `am start -S -W com.jingdong.app.mall/com.jingdong.app.mall.MainFrameActivity` 时直接报错：java.lang.SecurityException: Permission Denial: starting Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER], 京东这么小心，启动的必须是lancher）    

### [内存](https://developer.android.com/topic/performance/memory.html)  ###    

查看内存的方法[有很多](https://developer.android.com/studio/profile/investigate-ram.html)：    


```
1. dumpsys meminfo  
2. procrank   
3. top   
4. /proc/meminfo(系统内存)   
5. /proc/pid/meminfo(进程内存,Rss,Pss,
6. Dalvik Heap
```

为什么分这么多，因为android应用都是JAVA进程，JAVA进程内存的分配，都是交给Dalvik虚拟机来分的。so,需要计算Dalvik Heap.   

### 测试方法 ###


```
adb shell dumpsys meminfo
adb shell cat /proc/meminfo
```

哪些值是我们关心的呢?
**heap siez**,因为这是程序员可以控制的。     

android[官方](https://developer.android.com/topic/performance/memory.html)提供了一些注意事项，告诉我们应该怎么样写对内存优化的代码。包括：    

>监听低内存事件(Release memory in response to events)   
>注意当前可用内存(Check how much memory you should use)  
>谨慎使用`service`(Use services sparingly)  
>使用android系统优化的对象，比如SparseArray, SparseBooleanArray, and LongSparseArray.(Use optimized data containers)   
>...   



### [CPU](https://developer.android.com/studio/profile/am-cpu.html)  ###


cpu的统计可以通过查看文件：`/proc/stat`或某个进程的CPU占用情况`/proc/pid/stat`来计算。计算公式：   

`100-idle*100/total`    

一般选取某一时间段的idle,和total的diff值进行比较，因此公式就变成了：   

`100-idle_diff*100/total_diff`    


如果是用`adb shell top -m 10 -d 1`和采用我的[统计方法](https://github.com/myersguo/PythonExamples/blob/master/adb/utility/adb_helper.py)会发现，两者会有不同。因为统计的`时间段不同`，`方式不同`，造成统计结果不同，很正常，那到底采用什么数据呢？     

有时候可以采用95th percentile方法，去除top5%的最大数据，剩下的最大值就是我们要的数据。    

插个题外话：   

统计的一堆「垃圾」数据到底有没有用?   

数据只是数据，**数据分析**和**结论**才是我们需要的。比如cpu的统计趋势，






### 参考资料 ###

[https://developer.android.com/topic/performance/launch-time.html](https://developer.android.com/topic/performance/launch-time.html)  
[http://elinux.org/Android_Memory_Usage](http://elinux.org/Android_Memory_Usage)    
[http://www.importnew.com/14486.html](http://www.importnew.com/14486.html)   
[https://www.kernel.org/doc/Documentation/filesystems/proc.txt](https://www.kernel.org/doc/Documentation/filesystems/proc.txt)   
[http://man7.org/linux/man-pages/man5/proc.5.html](http://man7.org/linux/man-pages/man5/proc.5.html)   


