---
title: LeetCode 1104.Path In Zigzag Labelled Binary Tree
date: 2019-07-06 19:58:01
tags:
- 题解
- Medium
categories:
- LeetCode
---

# Question

In an infinite binary tree where every node has two children, the nodes are labelled in row order.In the odd numbered rows (ie., the first, third, fifth,...), the labelling is left to right, while in the even numbered rows (second, fourth, sixth,...), the labelling is right to left.

<!--more-->

![tree.png](/images/tree.png)

Given the label of a node in this tree, return the labels in the path from the root of the tree to the node with that label.

Example 1:
```
Input: label = 14
Output: [1,3,4,14]
```

Example 2:
```
Input: label = 26
Output: [1,2,6,10,26]
```
Constraints:
```
1 <= label <= 10^6
```

# Solution
首先根据`label`计算所在行数，根据行数奇偶可以判断这一行的数是否逆序。逆序则计算正序时对应的值，然后`//2`得到上一层正序时的值，再判断上一层层数的奇偶性，若上一层是偶数层，则将数字转换为逆序时的值。重复此过程直到根节点。

```python
import math
class Solution:
    def findOriginIndex(self, label):
        row = math.floor(math.log(label, 2) + 1)
        rowMin = pow(2, row - 1) 
        rowMax = pow(2, row) - 1
        sm = rowMin + rowMax
        return sm - label
    def pathInZigZagTree(self, label: int):
        if label == 1:
            return [1]
        ret = []
        ret.append(label)
        while label != 1:
            row = math.floor(math.log(label, 2) + 1)
            if row % 2 == 0:
                label = self.findOriginIndex(label)
                label //= 2
                ret.append(label)
                continue
            label //= 2
            label = self.findOriginIndex(label)
            ret.append(label)
        ret.reverse()
        return ret

a = Solution()
print(a.pathInZigZagTree(26))
```