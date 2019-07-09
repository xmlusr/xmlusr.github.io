---
title: 213. House Robber II
date: 2019-07-09 21:41:41
tags:
- 题解
- Medium
- 动态规划
categories:
- LeetCode
---

# Question
You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are arranged in a circle. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

<!--more-->

Example 1:
```
Input: [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2),
             because they are adjacent houses.
```

Example 2:
```
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

# Solution
和**House Robber I**的不同点就是房子是围城一个圈的，即若偷第一个房子，则最后一个房子不能偷了


第一个房子偷与不偷的两种情况如下：
1. 偷第一个房子,则最后一个不能偷了，即`nums[2:-1]`按照**House Robber I**求解, 结果再加上nums[0]
2. 不偷第一个房子，后面`nums[1:]按`照**House Robber I**求解

上面两种情况的最大值即为解。

Python:
```python
class Solution:
    def rrob(self, nums, i, j,dc):
        if (i,j) in dc:
            return dc[(i,j)]
        length = j-i
        if length == 0 :
            return 0
        if length <= 2:
            return max(nums[i:j])
        ans = max(self.rrob(nums,i+2,j,dc)+nums[i],self.rrob(nums,i+1,j,dc))
        dc[(i,j)] = ans
        return ans
    def rob(self, nums: List[int]) -> int:
        length = len(nums)
        if length == 0 :
            return 0
        if length<=3:
            return max(nums)
        ans = 0
        dc = dict()
        ans = max(self.rrob(nums,2,length-1,dc)+nums[0],self.rrob(nums,1,length,dc))
        return ans   
```