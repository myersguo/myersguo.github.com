---
layout: wp
title: leetcode
---


1. [two sum](https://leetcode.com/problems/two-sum/description/)   

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:

>Given nums = [2, 7, 11, 15], target = 9,  
>  
>Because nums[0] + nums[1] = 2 + 7 = 9,  
>return [0, 1].  


```
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        l = len(nums)
        for i in xrange(l):
            for j in xrange(i+1, l):
                if nums[i] + nums[j] == target:
                    return [i, j]
    def twoSum2(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        lookup = {}
        for i, num in enumerate(nums):
            if target - num in lookup:
                return [lookup[target - num], i]
            lookup[num] = i
        return []
        
```

2. Add Two Numbers

```
def addTwoNumbers(self, l1, l2):
        """
        :type l1: ListNode
        :type l2: ListNode
        :rtype: ListNode
        """
        if not l1:
            return l2
        if not l2:
            return l1
        dummy = ListNode(0)
        current, carry = dummy, 0

        while l1 or l2:
            val = carry
            if l1:
                val += l1.val
                l1 = l1.next
            if l2:
                val += l2.val
                l2 = l2.next
            carry, val = val / 10, val % 10
            current.next = ListNode(val)
            current = current.next

        if carry == 1:
            current.next = ListNode(1)

        return dummy.next 
```

3. Longest Substring Without Repeating Characters   

```
def lengthOfLongestSubstring(self, s):
        """
        :type s: str
        :rtype: int
        """
        longest, start, visited = 0, 0, [False for _ in xrange(256)]
        for i, char in enumerate(s):
            if visited[ord(char)]:
                while char != s[start]:
                    visited[ord(s[start])] = False
                    start += 1
                start += 1
            else:
                visited[ord(char)] = True
            longest = max(longest, i - start + 1)
        return longest
```

4.  Median of Two Sorted Arrays    

```
def findMedianSortedArrays(self, nums1, nums2):
        """
        :type nums1: List[int]
        :type nums2: List[int]
        :rtype: float
        """
        nums3 = nums1 + nums2
        nums3.sort()
        if len(nums3)%2 ==1 :
            return nums3[len(nums3)//2]
        else:
            return (float(nums3[len(nums3)/2])+float(nums3[len(nums3)/2-1]))/2
```

5. Longest Palindromic Substring   

```
def longestPalindrome(self, s):
        """
        :type s: str
        :rtype: str
        """
        def preProcess(s):
            if not s:
                return ['^', '$']
            T = ['^']
            for c in s:
                T +=  ['#', c]
            T += ['#', '$']
            return T

        T = preProcess(s)
        P = [0] * len(T) 
        center, right = 0, 0
        for i in xrange(1, len(T) - 1):
            i_mirror = 2 * center - i
            if right > i:
                P[i] = min(right - i, P[i_mirror])
            else:
                P[i] = 0

            while T[i + 1 + P[i]] == T[i - 1 - P[i]]:
                P[i] += 1

            if i + P[i] > right:
                center, right = i, i + P[i]       
        
        max_i = 0
        for i in xrange(1, len(T) - 1):
            if P[i] > P[max_i]:
                max_i = i
        start = (max_i - 1 - P[max_i]) / 2
        return s[start : start + P[max_i]] 
```



### 参考资料 ### 

[https://github.com/kamyu104/LeetCode](https://github.com/kamyu104/LeetCode)  

