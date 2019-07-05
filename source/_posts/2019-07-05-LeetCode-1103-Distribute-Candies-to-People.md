---
title: LeetCode 1103.Distribute Candies to People
date: 2019-07-05 21:29:02
tags:
- 题解
- Easy
categories:
- LeetCode
---

我们买了一些糖果 candies，打算把它们分给排好队的 n = num_people 个小朋友。给第一个小朋友 1 颗糖果，第二个小朋友 2 颗，依此类推，直到给最后一个小朋友 n 颗糖果。然后，我们再回到队伍的起点，给第一个小朋友 n + 1 颗糖果，第二个小朋友 n + 2 颗，依此类推，直到给最后一个小朋友 2 * n 颗糖果。重复上述过程（每次都比上一次多给出一颗糖果，当到达队伍终点后再次从队伍起点开始），直到我们分完所有的糖果。注意，就算我们手中的剩下糖果数不够（不比前一次发出的糖果多），这些糖果也会全部发给当前的小朋友。返回一个长度为 num_people、元素之和为 candies 的数组，以表示糖果的最终分发情况（即 ans[i] 表示第 i 个小朋友分到的糖果数）

<!--more-->

**Example 1:**

```
Input: candies = 7, num_people = 4
Output: [1,2,3,1]
```

**Example 2:**

```
Input: candies = 10, num_people = 3
Output: [5,2,3]
```

**Constraints:**

1 <= candies <= 10^9
1 <= num_people <= 1000

# Solution
第1次:
1 2 3 ... n (sum=one)

第2次：
1+n 2+n 3+n ... n+n (sum=one+n*n)

第3次:
1+2n 2+2n 3+2n ... n+2n (sum=one+2\*n\*n)

所以第i次：
1+(i-1)n 2+(i-1)n ... n+(i-1)n (sum=one+(i-1)\*n\*n)

首先计算能完整分发几次(总的不断减去sum判断余下的够不够下一个sum),将余下不足完成一次完整遍历的按上述规律一个个分发。

```python
class Solution:
    def distributeCandies(self, candies, num_people):
        one = (num_people+1)*num_people//2
        ret = []
        if one >= candies:
            need = 1
            i = 0 
            while candies >= need:
                ret.append(need)
                candies -= need
                need += 1
                i += 1
            if candies >0 :
                ret.append(candies)
                i += 1
            while i < num_people:
                ret.append(0)
                i += 1
            return ret
        need = one
        i = 0
        while candies >= need:
            candies -= need
            i+=1
            need = one + num_people*num_people*i
        tail = (i * i - i) // 2 * num_people 
        ret = [j * i + tail for j in range(1, num_people + 1)]
        tail *= num_people
        pre = num_people * i
        need = pre + 1
        i = 0
        while candies >= need:
            ret[i] += need
            i += 1
            candies -= need
            need += 1
        if candies > 0:
            ret[i] += candies
        return ret
```
