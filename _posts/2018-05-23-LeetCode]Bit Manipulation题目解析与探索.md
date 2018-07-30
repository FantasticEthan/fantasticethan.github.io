---
title: "[LeetCode]Bit Manipulation题目解析与探索"
layout: post
date: 2018-05-23 10:41
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Leetcode
category: blog
author: ethan
description: 对于Bit Manipulation问题的解析与探索


---

## Bit Manipulation 位运算

Leetcode中Bit Manipulation类问题，基本都利用位运算解决，效率很高，但是trick没做过的话会很难想。

### 位运算基本操作

基本操作是位运算的基础。

- 设置交集 A &#124; B
- 设置并集 A & B
- 减法操作 A & ~B
- 非操作 ~A
- 设置A的某位为‘1’,A &#124;= 1&lt;&lt;bit
- 清除A的某位, A&~(1&lt;&lt;bit)
- 测试A的某位是否为0或1, (A & 1 &lt;&lt; bit )!=0
- 提取最后一个'1', A&-A(注意这里是补码) 或者 A & ~(A-1) 或者 A^(A&(A-1))
- 消除A的最后一个‘1’, A&(A-1)
- 获得全‘1’,  ~0

---

先对典型问题分析，Single Number问题。

### Single Number
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

算法的第一部xm~x1部分。

创造m位进行计数，因为是计数器，所以**2^m>=k,即 m >= logk**对于32位数，我们就可以利用m个32位整数进行计数，替代到32个m位数来进行计数。

算法的第二部分mask。

计数结束，要是数字到达K之后变为0，这里有两个问题，第一k如何表示，第二如何保证计数器每次都能屏蔽掉k（到达k之后初始化为0）。对于第一个问题，k值得编码同样利用位数来表示，**y1 & y2 & ... & ym** 代表着k的编码状态。对于第二个问题，编码取反与计数器按位取并可以切割掉k（原因？）。

算法的第三部分return。返回值因为其他到达k之后都被切割掉，则只有剩余的出现p次的那个数。
{% highlight python %}
Since p = 3, in binary form p = '011', then p1 = p2 = 1, so we can return either x1 or x2. 
If p = 4, in binary form p = '100', only p3 = 1, which implies we can only return x3.Or alternatively we can simply return (x1 | x2 | x3).
{% endhighlight %}

于是对于Single Number的前两个问题，给出算法案例。

- **k=2,p=1**

	求出m=1,此时2^m=k
	{% highlight python %}
	def singleNumber(self, A):
	    return reduce(operator.xor, A)
	{% endhighlight %}

- **k=3,p=1**

	求出m=2,此时2^m>k,需要mask
	{% highlight python %}
	class Solution:
	    def singleNumber(self, nums):
	        """
	        :type nums: List[int]
	        :rtype: int
	        """
	        x1=0;x2=0;mask=0
	        for i in nums:
	            x2 ^= (x1&i)
	            x1 ^= i
	            mask = ~(x1&x2)
	            x2 &= mask
	            x1 &= mask
	        return x1
	{% endhighlight %}

- **第三类问题**

   有两个元素出现一次，其他所有元素出现两次。首先找出亦或，对于亦或有三个公式问题的关键在于找到亦或之后的码，以最右边的‘1’为flag，将数据分为两部分，因为两个数字不同且都出现一次，所以必定分在这不同的两部分。
	
	- $a\oplus 0=a$
	- $a\oplus a=0$
	- $a\oplus b\oplus a= (a\oplus a)\oplus b=0 \oplus b = b$
	
{% highlight python %}
from functools import reduce
import operator
class Solution:
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        x_xor_y = reduce(operator.xor,nums)
        x_xor_y &= -x_xor_y #原码与补码取并，求得右边第一个‘1’
        result = [0,0] #将两个数以‘1’为flag分成两部分
        for i in nums:
             result[bool(i & x_xor_y)] ^= i
        return result
{% endhighlight %}

### Missing nums , Find the Difference，Set Mismatch

missing num的问题解法和single num类似，关键在于原始list index与list value的亦或，亦或操作之后，只剩下没有value的index值，该值为missing num。

同样是找不同，只要把相同的进行亦或操作消除掉，剩下的就是不同元素。Find Difference多一个字符转换问题。来一个one-line code吧。

Set Mismatch同样是找不同，先利用亦或找到不同的两个值。然后判断是否存在原始数组，从而调换顺序。

{% highlight python %}
class Solution:
    def findTheDifference(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: str
        """
        return chr(reduce(operator.xor, map(ord, s), 0) ^ reduce(operator.xor, map(ord, t), 0))
{% endhighlight %}
            

### Number Of 1-bits ,Bitwise AND of Numbers Range
这个问题的解决方法在于理解**n &= n - 1**

当数字减掉1之后在于原数字取按位与，则最后一个‘1’就被清除了。在while n的前提下，清除几次就统计出有几个‘1’。同理，对于第二个问题，求出他们的bitwise and 就是求出他们前几个相同位，在while m<=n的过程中，不断清除最后一个‘1’，剩下的就是他们的相同位。

### Power of Two,Power of Four

同理，基本操作在于**n &= n - 1**。因为2，只有一个‘1’，清除之后应该为0。同理，对于Power of Four,多加一个判断是否‘1’的位置在奇数为上，用&0b01010101010101010101010101010101来判断。

### Maximum Product of Word Lengths 

这一题是想了很久都没想出来。问题的关键在于将26个字符以二进制32位数做成一个类哈希表。用一个int，32位；而小写字母只有26个，后26位用来表示对应的字符是否存在。然后每次两个int 按位与，如果结果为0，则没有相同字符，则作为候选最大product.

{% highlight python %}
class Solution:
    def singleNumber(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        words.sort(key=lambda x: len(x), reverse=True)
        print(words)
        bits = [0] * len(words)
        for i, word in enumerate(words):
            for c in word:
                bits[i] |= (1 << (ord(c) - ord('a')))

        max_product = 0
        for i in range(len(words) - 1):
            if len(words[i]) ** 2 <= max_product:
                break
            for j in range(i + 1, len(words)):
                if len(words[i]) * len(words[j]) <= max_product:
                    break
                if not (bits[i] & bits[j]):
                    max_product = len(words[i]) * len(words[j])
        return max_product
{% endhighlight %}


### Maximum XOR of Two Numbers in an Array 

问题要求出两个数最大的XOR。直接遍历，存储最大XOR效率是$O(n)$。
利用亦或的特性，$a \oplus b = c$,则$a \oplus c = b$。同理，亦或最大应该是此位置上的bit为1。于是，可以推得$a \oplus b=1$,则$b \oplus 1 =a$。如果值不在set里面，则肯定为0。

{% highlight python %}
class Solution:
    def findMaximumXOR(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        result = 0
        for i in reversed(range(32)):
            result <<= 1
            prefixes = set()
            for n in nums:
                prefixes.add(n >> i)
            for p in prefixes:
                if (result | 1) ^ p in prefixes:
                    result += 1
                    break
        return result 

{% endhighlight %}

### Hamming Distance,Total Hamming Distance

两道题都是求汉明距离。对于第一题，汉明距离求出亦或之后计算bit为1的数量很容易求出。对于总汉明距离，如何降低时间复杂度是关键。因为bit的值都为1和0，所以对于数字的每个bit位置，只要计算bit为1和0的数量相乘，就是当前位置的总汉明距离。之后，对于32个bit位置求和。即得总汉明距离。One Line Code。

{% highlight python %}
class Solution:
    def totalHammingDistance(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        return sum(b.count('0') * b.count('1') for b in zip(*map('{:032b}'.format, nums)))
{% endhighlight %}     
                
### Binary Number with Alternating Bits

这一题本来是很简单的问题。但是做了很久。问题在于判断当前bit位置之后，下一个位置的0到1的变化我用了补码(引以为戒)。应该用亦或。这样可以把0替换成1，把1替换成0。问题不过是位置的逐个判断。判断当前位置是否和上一位仙童。贴上代码吧。

{% highlight python %}
class Solution:
    def hasAlternatingBits(self, n):
        """
        :type n: int
        :rtype: bool
        """
        d = n&1
        while (n&1)==d:
            d^=1
            n >>=1
        return n==0
{% endhighlight %}  
        
---

### 小结

位运算的题目主要在于对原始数据之后转换成32位bit后进行位运算。操作主要是求交集，并集，选取哪一位，选取最后一个bit为1的数之类。理解0，1关系是关键。还有一个小技巧，很多位运算的题可以利用求和之后，加加减减，才提取出需要的值。在想不出来的时候可以尝试下。总共做了22题。有些没放出来，主要是bit位置判断的，例如UTF-8 Validation和Binary Watch。


