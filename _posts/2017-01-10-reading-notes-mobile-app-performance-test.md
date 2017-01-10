---
layout: wp
title: 读书笔记---移动APP性能评测与优化
---

### 内存  ###   

工具：Android Device Monitor, Eclipse Memory Analyzer Tool(MAT),dumpsys meminfo <package name | pid>,


测试用例：   

* 应用启动后的内存   
* 图片加载前后   
* 多张图片显示后  
* 长时间运行程序后   
* 反复滑动，操作后   

 举个例。  

执行`dumpsys meminfo`:  

```
Applications Memory Usage (kB):
Uptime: 61072414 Realtime: 112740389

Total PSS by process:
   196458 kB: com.tencent.mm (pid 1439 / activities)
   145538 kB: com.android.systemui (pid 4001)
   144863 kB: com.sina.weibo (pid 5940 / activities)
   118804 kB: system (pid 2143)
   106800 kB: com.jingdong.app.mall (pid 4869 / activities)
    87494 kB: com.miui.home (pid 4767 / activities)
    54476 kB: com.baidu.input (pid 4603)
    48683 kB: com.android.incallui (pid 4904 / activities)
    48112 kB: com.android.phone (pid 4725)
    41134 kB: com.eg.android.AlipayGphone (pid 3994)
...
Total PSS by OOM adjustment:
   126279 kB: Native
   383021 kB: Persistent
   130946 kB: Foreground
   175840 kB: Visible
   483224 kB: Perceptible
    90354 kB: A Services
   586963 kB: B Services
   161741 kB: Cached

Total PSS by category:
   976304 kB: Dalvik
   613015 kB: Native
    97037 kB: .dex mmap
    94832 kB: Gfx dev
    86174 kB: Dalvik Other
    82820 kB: .art mmap
    64514 kB: .so mmap
```

PSS:[Proportinal Set Size](https://en.wikipedia.org/wiki/Proportional_set_size),内存占用。      
OOM:Out of Memory,内存溢出。   

```
vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
 4  0      0 104752  28044 357052    0    0  8791   422    3 4081 25 17 55  3
```


### 耗电量 ###   

测试工具：    

Battery Historian,adb shell dumpsys batteryinfo/batterystats,

测试用例：    

* 后台待机耗电量(有网，无网)   
* 联网方式切换   
* 横竖屏切换   


### FPS ###   

(未完，待续）  


* 
