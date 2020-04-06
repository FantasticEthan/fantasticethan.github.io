---
title: "[LeetCode]Array 题目解析与探索(1)"
layout: post
date: 2018-05-25 17:01
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Leetcode
- Array
- Solution
category: blog
author: ethan
description: 对于Array问题的解析与探索

---

## Array 数组	

{% highlight python %}
class Solution:
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums,result,i = sorted(nums),[],0
        while i < len(nums)-2:
            if i == 0 or nums[i] != nums[i - 1]: #
                j,k = i+1,len(nums)-1
            while j<k:
                if (nums[i]+nums[j]+nums[k])>0:
                    k-=1
                elif(nums[i]+nums[j]+nums[k])<0:
                    j+=1
                else:
                    result.append([nums[i],nums[j],nums[k]])
                    j, k = j + 1, k - 1
                    while j<k and nums[j]==nums[j-1]:
                        j+=1
                    while j<k and nums[k]==nums[k+1]:
                        k-=1
            i+=1
        return result
{% highlight python %}