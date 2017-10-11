---
layout: wp
title: 日活统计遇到的疑惑
---

最近有个需求是需要统计某个版本的安装数量，之前接触过类似的需求，使用 redis 的bitmap 来记录，比如user_id是11位的,比如我们统计一下：  

```
setbit today 4294967295 1
setbit today 1 1
redis-cli --bigkeys

统计显示:Biggest string found 'today' has 536870912 bytes,4G
```

因为 bit offset 最大是2^32，即 4294967295, 10位，我们的一般ID都是11位，那怎么统计？   

我的做法是 offset%1000 放到key后缀,进行统计是否统计过，单独计数来统计数量：  

```
prefix = offset % 100
offset = offset / 100
setbit today: prefix  offset 1
incr today:cnt 1
这样，最大是数能统计到400(12位)亿，资源占用就是 4G*100,就是100G
```

如果要区分终端，区分版本统计日活，那岂不是 100G * xxx 倍？   
这个占用量也很大啊？又没有更好的办法呢？  

解决：  
从这边文章：[http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html](http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html)


>Sparse bitmaps can be compressed in order to gain space efficiency, but that is not always helpful.

找到答案，使用Hyper LogLog,可以满足需求：  

```
pfadd today 1
pfadd today 2
统计: pfcount today
```

占用空间只有:  
12304 bytes, 12k

完美解决啊，基于 hyper loglog是什么，可以看上面的文章详细了解。   

