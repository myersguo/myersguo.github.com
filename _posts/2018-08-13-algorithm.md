---
layout: wp
title: algorithm
private: true
comments: true
---

### 用两个堆栈模拟队列的功能: push\pop\count ###

```
def stack_push(a, b, item):
	"""
	入队操作，判断 a 是否为空，为空则将b中数据压入a,不为空，则直接入栈
	"""
	if len(a) <= 0:
		while len(b) > 0:
			a.append(b.pop())
	a.append(item)
			
def stack_pop(a, b):
	"""
	出队操作：判断 b 是否为空，不为空直接弹出;为空则将a压入b中
	"""
	if len(b) >0:
		return b.pop()
	while len(a) > 1:
		b.append(a.pop())
	if len(a) > 0:
		return a.pop()

def stack_count(a, b):
	return len(a) if len(b) <=0 else len(b)

a, b = [], []
if __name__ == "__main__":
        stack_pop(a, b)
        stack_push(a, b, 1)
        stack_push(a, b, 3)
        stack_push(a, b, 5)
        assert stack_count(a, b) == 3
        assert stack_pop(a, b) == 1
        assert stack_pop(a, b) == 3
        assert stack_count(a, b) == 1
        stack_push(a, b, 4)
        assert stack_count(a, b) == 2
        assert stack_pop(a, b) == 5
        assert stack_pop(a, b) == 4
```

### 二叉树最长路径 ###


```
# 二叉树节点
class Node(object):
	def __init__(self, elem=None, lChild=None, rChild=None):
		self.elem = elem
		self.lChild = lChild
		self.rChild = rChild
		self.parent = None
        def __str__(self):
            if self.elem:
                return str(self.elem)


# 二叉树最长路径
def find_tree_max_path(root):
	"""
	层次遍历二叉树，对每个节点进行标记，标记其父节点
	"""
	treeList = []
	treeList.append(root)
	_tmp, index = treeList[0], 0
	while True:
		if _tmp.lChild is not None:
			treeList.append(_tmp.lChild)
                        print 'add new item', _tmp.lChild
			_tmp.lChild.parent = _tmp
		if _tmp.rChild is not None:
			treeList.append(_tmp.rChild)
                        print 'add new item', _tmp.rChild
			_tmp.rChild.parent = _tmp
		index +=1
                if index >= len(treeList):
                    break
		_tmp = treeList[index]

        # 输出最长路径
        item = treeList[len(treeList)-1]
        while item:
            print item
            item = item.parent
if __name__ == "__main__":
	a, b, c, d, e, f, g = Node(1), Node(2), Node(3),Node(4),Node(5),Node(6),Node(7)
	a.lChild, a.rChild = b, c
	b.lChild, b.rChild = d, e
	e.lChild, e.rChild = f, g
	find_tree_max_path(a)
```

### android 解锁密码 ###


```
from itertools import permutations, chain
impossible = {'91': '5', '13': '2', '17': '4', '46': '5', '37': '5', '31': '2', '28': '5', '39': '6', '19': '5', '64': '5', '97': '8', '71': '4', '82': '5', '73': '5', '93': '6', '79': '8'}

# 生成所有的组合类型
allIterate = chain(*(permutations('123456789', i) for i in range(4, 10)))
count = 0
for item in allIterate:
	stri = ''.join(item)
	for k, v in impossible.items():
    	if k in stri and v not in stri[:stri.find(k)]:
                break
        else:
            count += 1
```


### 出现此处大于 m 的 数组元素 ###

```
a = [1, 1, 3, 1, 2, 3, 1, 2, 3, 4, 5, 4, 5, 4, 1]

def findArr(a, m=3):
    result = []
    hashSet = {}
    for item in a:
        if hashSet.get(item):
            hashSet[item] +=1
        else:
            hashSet[item] = 1
    for item in hashSet:
        if hashSet[item] > m:
            result.append(item)
    return result
print findArr(a, 4)
```

假如我们期望 hashSet 的空间尽量小，则：   

```
def findArr2(a, m=3):
    result, hashSet = [], {}
    lenA = len(a)
    lenH = lenA / m
    for item in a:
        if hashSet.get(item) is not None:
            if hashSet[item] <=0:
                del hashSet[item]
                continue
            if len(hashSet) > lenH:
                for _item in hashSet:
                    hashSet[_item] -=1
            hashSet[item] += 1
        else:
            hashSet[item] = 1
    for item in hashSet:
        if hashSet[item] > m:
            result.append(item)
    return result

```

### 有两个字典，分别存有 100 条数据和 10000 条数据，如果用一个不存在的 key 去查找数据，在哪个字典中速度更快？ ###



hash 表的满的程度用负载因子体现。负载因子越大，意味着哈希表越满，越容易导致冲突，性能也就越低。因此，一般来说，当负载因子大于某个常数(可能是 1，或者 0.75 等)时，哈希表将自动扩容。[参考](https://bestswifter.com/hashtable/)。hash 扩容 rehash 过程一般2倍空间的申请，将原来的数据复制到新的空间。   

redis的扩容,详见：[渐进式hash](http://redisbook.com/preview/dict/incremental_rehashing.html)     




