---
layout: wp
title: 大数据计数
---

之前在[日活统计的烦恼](http://myersguo.me/2017/10/11/redis-bitmap.html)中说，有一个需求是要统计数量的。起初是想从 redis bitmap set 来统计，后来发现 bitmap 的统计上线越大占用的存储空间越大。而最终的结果是， bit 位将变得非常稀松。



想要让数据统计**均匀分布**，我们首先想到是通过某种 hash 函数，让位置能均匀的分布在 n 位以内。但这样仍然占用较大的空间（10亿，1G）。在这篇2014的文章：[[Big Data Counting: How To Count A Billion Distinct Objects Using Only 1.5KB Of Memory](http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html)] 提到**基数估计算法（** [cardinality](http://www.google.com/url?q=http%3A%2F%2Fdblab.kaist.ac.kr%2FPublication%2Fpdf%2FACM90_TODS_v15n2.pdf&sa=D&sntz=1&usg=AFQjCNHtGk7728Hnh8XgbMFCEQwDMNd1kw) [estimation](http://www.google.com/url?q=http%3A%2F%2Falgo.inria.fr%2Fflajolet%2FPublications%2FDuFl03.pdf&sa=D&sntz=1&usg=AFQjCNHij4lkOvtFkqLQ7BwDCOX3DHx2IQ) **）**,



其中一种基数估计方法为[Linear Counting](http://blog.codinglabs.org/articles/algorithms-for-cardinality-estimation-part-ii.html)：



>LC的基本思路是：设有一哈希函数H，其哈希结果空间有m个值（最小值0，最大值m-1），并且哈希结果服从均匀分布。使用一个长度为m的bitmap，每个bit为一个桶，均初始化为0，设一个集合的基数为n，此集合所有元素通过H哈希到bitmap中，如果某一个元素被哈希到第k个比特并且第k个比特为0，则将其置为1。当集合所有元素哈希完成后，设bitmap中还有u个bit为0。则：
>
>n̂ =−mlog(u/m) 为n的一个估计，且为最大似然估计（MLE）。Linear Counting算法相较于直接映射bitmap的方法能大大节省内存（大约只需后者1/10的内存），但毕竟只是一个常系数级的降低，空间复杂度仍然为O(Nmax)O(Nmax)。



**[LogLog Counting](http://blog.codinglabs.org/articles/algorithms-for-cardinality-estimation-part-iii.html)**（以下简称LLC）出自论文“Loglog Counting of Large Cardinalities”。LLC的空间复杂度仅有O(log2(log2(Nmax)))O(log2(log2(Nmax)))，使得通过KB级内存估计数亿级别的基数成为可能，因此目前在处理大数据的基数计算问题时，所采用算法基本为LLC或其几个变种。

它将哈希空间平均分成m份，每份称之为一个桶（bucket）。对于每一个元素，**其哈希值的前k比特作为桶编号**，其中2k=m2k=m，而**后L-k个比特作为真正用于基数估计的比特串**。桶编号相同的元素被分配到同一个桶，在进行基数估计时，首先计算每个桶内元素最大的第一个“1”的位置，设为M[i]，然后对这m个值取平均后再进行估计，即：

n̂ =21m∑M[i]n^=21m∑M[i]

这相当于物理试验中经常使用的多次试验取平均的做法，可以有效消减因偶然性带来的误差。

> 假设H的哈希长度为16bit，分桶数m定为32。设一个元素哈希值的比特串为“0001001010001010”，由于m为32，因此前5个bit为桶编号，所以这个元素应该归入“00010”即2号桶（桶编号从0开始，最大编号为m-1），而剩下部分是“01010001010”且显然ρ(01010001010)=2ρ(01010001010)=2，所以桶编号为“00010”的元素最大的ρρ即为M[2]的值。



在[这篇文章](http://blog.notdot.net/2012/09/Dam-Cool-Algorithms-Cardinality-Estimation)中提到的一个近似估计的算法：

```
def trailing_zeroes(num):
  """Counts the number of trailing 0 bits in num."""
  if num == 0:
    return 32 # Assumes 32 bit integer inputs!
  p = 0
  while (num >> p) & 1 == 0:
    p += 1
  return p

def estimate_cardinality(values, k):
  """Estimates the number of unique elements in the input set values.
  Arguments:
    values：An iterator of hashable elements to estimate the cardinality of.
    k：The number of bits of hash to use as a bucket number; there will be 2**k buckets.
  """
  num_buckets = 2 ** k
  max_zeroes = [0] * num_buckets
  for value in values:
    h = hash(value)
    bucket = h & (num_buckets - 1) # Mask out the k least significant bits as bucket ID
    bucket_hash = h >> k
    max_zeroes[bucket] = max(max_zeroes[bucket], trailing_zeroes(bucket_hash))
  return 2 ** (float(sum(max_zeroes)) / num_buckets) * num_buckets * 0.79402


[5/estimate_cardinality([random.random() for i in range(5)], 10) for j in range(10)]
```

其中[翻译稿解释](http://blog.jobbole.com/78255/):

> **我们保持一个计算前导(或尾部)0个数的数组，然后在最后对个数求平均值**，如果我们的平均值是 x，我们的估计就是 2^x 乘以桶的个数。前面没有说到 的是这个魔术数 0.79402。数据统计表明我们的程序存在一个可预测的偏差，它会给出一个比实际更大的估计值。这个在 Durand-Flajolet 的论文中导出的魔术常数是用来修正这个偏差的。实际上这个数字随着使用的桶的个数(最大2^64)而发生变化，但是对于更多数目的桶数，它会收敛到我们上面用到的算法的估计数字。
>
> 这个程序给了我们一个非常好的估计，对于 m 个桶来说，平均错误率大概在 1.3/sqrt(m) 左右。所以1024个桶时()，我们大概会有 4% 的期望错误率。为了估计每篇最多 2^27 个数据的数据集每个桶仅需要 5 比特就够了。少于 1 kb 内存，这真的很赞(1024 * 5 = 5120，即 640 字节)!




[http://blog.notdot.net/2012/09/Dam-Cool-Algorithms-Cardinality-Estimation](http://blog.notdot.net/2012/09/Dam-Cool-Algorithms-Cardinality-Estimation)  
[hyperloglog](http://algo.inria.fr/flajolet/Publications/FlFuGaMe07.pdf)  
http://blog.jobbole.com/78255/    
http://blog.codinglabs.org/articles/cardinality-estimate-exper.html#ref1

