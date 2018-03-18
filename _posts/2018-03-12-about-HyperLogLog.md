---
layout: wp
title: About HyperLogLog
comments: true
---

### 统计 ###

我们之前谈到过[日活统计](https://myersguo.github.io/2017/10/15/redis-bitmap-2.html)的问题:  

假如直接用集合统计`sadd uv user_1`,那么要存储 1亿数据的用户数据，占用内存将达到**1.02G**(uid 按照11位存储,11*8*100000000)。如果采用 bitmap 的形式 `SETBIT uv 1 1`，那么存储1亿数据，内存将需要12M( 100000000 bit), 大大节省了空间。但是，如果 user_id 不稀疏，11 位的uid 将占用1.16G (10000000000) 空间。从之前的文章，我们知道在大数统计上，使用**HyperLogLog**算法来统计占用空间将大大缩短。我们来解开它的面纱。   


### 基数估算 ###

上面我们提到，11 位的 uid 的 bit 存储大概占用1.16G，能表示 100亿的数据，但是我们通过的用户数都在10亿以内，我们仅仅使用10位bit就可以表示。那我们的为题就变成了，如果用10位bit来表示最大100亿的10以内的数据。   

显然，如果有一个 hash 函数，将100亿的数据，均匀的 hash 到10位 bit 内即可。   

1990年，一篇「A linear-time probabilistic counting algorithm for database applications」提出这种线性估算法。   


>设有一哈希函数H，其哈希结果空间有m个值（最小值0，最大值m-1），并且哈希结果服从均匀分布。使用一个长度为m的bitmap，每个bit为一个桶，均初始化为0，设一个集合的基数为n，此集合所有元素通过H哈希到bitmap中，如果某一个元素被哈希到第k个比特并且第k个比特为0，则将其置为1。[link](http://blog.codinglabs.org/articles/algorithms-for-cardinality-estimation-part-ii.html)   

[这里](https://gist.github.com/devdazed/3873524) or [这里](https://github.com/addthis/stream-lib/blob/master/src/main/java/com/clearspring/analytics/stream/cardinality/LinearCounting.java)有实现的例子。   


### HyperLogLog ###

这篇[blog](http://content.research.neustar.biz/blog/hll.html)演示了 hyperloglog 算法的过程：  

初始化一个长度为m（64）的数组，将待统计的集合分为m组, 每组的统计值放到对应的数组位置上。   

输入 value, 通过 hash 得到 hash value;  
hash value 的最后24位用二进制表示;  
二进制bit位最后r位bit位，表示落在数组（桶）的位置index。  
从第r+1位开始，第一个1出现的值,放到第index个位置。   

>分桶平均的基本原理是将统计数据划分为mm个桶，每个桶分别统计各自的{k_{max}}k


>就是将哈希空间平均分成m份，每份称之为一个桶（bucket）。对于每一个元素，其哈希值的前k比特作为桶编号，其中2^k=m，而后L-k个比特作为真正用于基数估计的比特串。桶编号相同的元素被分配到同一个桶，在进行基数估计时，首先计算每个桶内元素最大的第一个“1”的位置，设为M[i]，然后对这m个值取平均后再进行估计，即：
>  
>n̂ =2^(1/m∑M[i])
>这相当于物理试验中经常使用的多次试验取平均的做法，可以有效消减因偶然性带来的误差。
>



### 参考资料 ###

[http://blog.codinglabs.org/articles/algorithms-for-cardinality-estimation-part-iii.html](http://blog.codinglabs.org/articles/algorithms-for-cardinality-estimation-part-iii.html)   
[pyhasher](https://github.com/flier/pyfasthash)  
[http://www.rainybowe.com/blog/2017/07/13/%E7%A5%9E%E5%A5%87%E7%9A%84HyperLogLog%E7%AE%97%E6%B3%95/index.html](http://www.rainybowe.com/blog/2017/07/13/%E7%A5%9E%E5%A5%87%E7%9A%84HyperLogLog%E7%AE%97%E6%B3%95/index.html)   


