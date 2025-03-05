---
title: "LeetCode - Day 12"
date: 2025-03-05
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
Given the root of a binary tree, flatten the tree into a "linked list":

The "linked list" should use the same TreeNode class where the right child pointer points to the next node in the list and the left child pointer is always null.
The "linked list" should be in the same order as a pre-order traversal of the binary tree.
 

Example 1:


Input: root = [1,2,5,3,4,null,6]
Output: [1,null,2,null,3,null,4,null,5,null,6]
Example 2:

Input: root = []
Output: []
Example 3:

Input: root = [0]
Output: [0]
 

Constraints:

The number of nodes in the tree is in the range [0, 2000].
-100 <= Node.val <= 100
 

Follow up: Can you flatten the tree in-place (with O(1) extra space)?
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
    def flatten(self, root):
        """
        :type root: Optional[TreeNode]
        :rtype: None Do not return anything, modify root in-place instead.
        """
        node = root
        if node is None:
            return

        stack = []
        queue = []
        stack.append(node)
        while not len(stack) == 0:
            node = stack.pop()
            # visit
            queue.append(node)

            if node.right is not None:
                stack.append(node.right)
            
            if node.left is not None:
                stack.append(node.left)

        for i in range(len(queue) - 1):
            queue[i].left = None
            queue[i].right = queue[i+1]

        queue[-1].left = None
        queue[-1].right = None
```

# Submission
```
Runtime
0
ms
Beats
100.00%


Memory
12.80
MB
Beats
42.74%
```
