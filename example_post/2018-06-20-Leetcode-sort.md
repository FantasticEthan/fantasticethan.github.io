---
title: "[LeetCode] Sort 题目解析与探索(1)"
layout: post
date: 2018-06-20 12:00
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Leetcode
- Sort
- Solution
category: blog
author: ethan
description: 对于Array问题的解析与探索

---

## Sort 排序问题

### 基本排序算法	

Merge Intervals

{% highlight python %}
Definition for an interval.
class Interval:
	def __init__(self, s=0, e=0):
        self.start = s
        self.end = e

class Solution:
    def merge(self, intervals):
        """
        :type intervals: List[Interval]
        :rtype: List[Interval]
        """
        def merge(left,right):
            result = []
            
            while(len(left)>0 and len(right)>0):
                if left[0].start<=right[0].start:
                    result.append(left.pop(0))
                else:
                    result.append(right.pop(0))
            result+=left
            result+=right
            
            return result
            
        def mergesort(uselist):
            if len(uselist)==1:
                return uselist
            
            mid = len(uselist)//2 #interger div
            left = uselist[0:mid]
            
            right = uselist[mid:]
            
            l1 = mergesort(left)
            r1 = mergesort(right)
            
            return merge(l1,r1)
            
        if not intervals: #not intervals is false
            return intervals
        intervals = mergesort(intervals)
        result = [intervals[0]]
        for i in range(1, len(intervals)):
            prev, current = result[-1], intervals[i]
            if current.start <= prev.end:
                prev.end = max(prev.end, current.end)
            else:
                result.append(current)
        return result
        
{% endhighlight %}

Insert Interval

{% highlight python %}

class Solution:
    def insert(self, intervals, newInterval):
        """
        :type intervals: List[Interval]
        :type newInterval: Interval
        :rtype: List[Interval]
        """
        result = []
        i = 0
        while i < len(intervals) and newInterval.start > intervals[i].end:
            result.append(intervals[i])
            i += 1
        while i < len(intervals) and newInterval.end >= intervals[i].start:
            newInterval = Interval(min(newInterval.start, intervals[i].start), \
                                   max(newInterval.end, intervals[i].end))
            i += 1
        result += newInterval,
        result += intervals[i:]
        return result
        
{% endhighlight %}
