---
title: "[Leetcode]Bit Manipulation题目解析与探索"
layout: post
date: 2018-05-23 10:41
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Leetcode
- Bit Manipulation
- Solution
category: blog
author: ethan
description: 对于Bit Manipulation问题的解析与探索
# jemoji: '<img class="emoji" title=":ramen:" alt=":ramen:" src="https://assets.github.com/images/icons/emoji/unicode/1f35c.png" height="20" width="20" align="absmiddle">'
---

## 题目:

Leetcode中Bit Manipulation类问题，基本都利用位运算解决，效率很高，但是trick没做过的话会很难想。先对典型问题分析，Single Number问题。

#### Especial Elements
- [Single Number](https://leetcode.com/problems/single-number/)
- [Single Number II](https://leetcode.com/problems/single-number-ii/)
- [Single Number III](https://leetcode.com/problems/single-number-iii/)

Single Number II是第一个问题的拓展，数字出现的次数从2次变成3次。问题可以归纳为：**给定一个整数数组，每个元素都出现k（k > 1）次，除了一个出现p次（p >= 1, p % k != 0），找到那一个**。

首先放出算法流程：

{% highlight code %}
for (int i : nums) {
    xm ^= (xm-1 & ... & x1 & i); 
    xm-1 ^= (xm-2 & ... & x1 & i);
    .....
    x1 ^= i;
    
    mask = ~(y1 & y2 & ... & ym) where yj = xj if kj = 1, and yj = ~xj if kj = 0 (j = 1 to m).

    xm &= mask;
    ......
    x1 &= mask;
}
{% endhighlight %}

算法的第一部xm~x1部分。创造m位进行计数，因为是计数器，所以<span class="evidence">2^m>=k,即 m >= logk</span> 对于32位数，我们就可以利用m个32位整数进行计数，替代到32个m位数来进行计数。
算法的第二部分mask。计数结束，要是数字到达K之后变为0，这里有两个问题，第一k如何表示，第二如何保证计数器每次都能屏蔽掉k（到达k之后初始化为0）。对于第一个问题，k值得编码同样利用位数来表示，<span class="evidence">y1 & y2 & ... & ym</span> 代表着k的编码状态。对于第二个问题，编码取反与计数器按位取并可以切割掉k（原因？）。
算法的第三部分return。返回值因为其他到达k之后都被切割掉，则只有剩余的出现p次的那个数。
{% highlight code %}
Since p = 3, in binary form p = '011', then p1 = p2 = 1, so we can return either x1 or x2. 
If p = 4, in binary form p = '100', only p3 = 1, which implies we can only return x3.Or alternatively we can simply return (x1 | x2 | x3).
{% endhighlight %}

---

## Evidence

You can try the evidence!

<span class="evidence">Paragraphs can be written like so. A paragraph is the basic block of Markdown. A paragraph is what text will turn into when there is no reason it should become anything else.</span>

{% highlight html %}
<span class="evidence">Paragraphs can be written like so. A paragraph is the basic block of Markdown. A paragraph is what text will turn into when there is no reason it should become anything else.</span>
{% endhighlight %}

---


