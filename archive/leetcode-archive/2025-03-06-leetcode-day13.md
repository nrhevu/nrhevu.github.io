---
title: "LeetCode - Day 13"
date: 2025-03-06
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
199. Binary Tree Right Side View
Solved
Medium

Topics
Companies
Given the root of a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

 

Example 1:

Input: root = [1,2,3,null,5,null,4]

Output: [1,3,4]

Explanation:



Example 2:

Input: root = [1,2,3,4,null,null,null,5]

Output: [1,3,4,5]

Explanation:



Example 3:

Input: root = [1,null,3]

Output: [1,3]

Example 4:

Input: root = []

Output: []

 

Constraints:

The number of nodes in the tree is in the range [0, 100].
-100 <= Node.val <= 100
```

# Solution
```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution(object):
    def rightSideView(self, root):
        """
        :type root: Optional[TreeNode]
        :rtype: List[int]
        """
        node = root

        if node is None:
            return []

        stack = []
        d = {}
        stack.append((0, node))

        while stack:
            level, node = stack.pop()

            if node.right:
                stack.append((level + 1, node.right))

            if node.left:
                stack.append((level + 1, node.left))

            d[level] = node.val

        return list(d.values())
            
```

# Submission
```
Runtime
0 ms
Beats
100.00%

Memory
12.46 MB
Beats
62.53%

```