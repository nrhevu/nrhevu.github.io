---
title: "LeetCode - Day 11"
date: 2025-03-04
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
2. Add Two Numbers
Medium

Topics
Companies
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

 

Example 1:


Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
Example 2:

Input: l1 = [0], l2 = [0]
Output: [0]
Example 3:

Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]
 

Constraints:

The number of nodes in each linked list is in the range [1, 100].
0 <= Node.val <= 9
It is guaranteed that the list represents a number that does not have leading zeros.
```

# Solution
```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: Optional[ListNode]
        :type l2: Optional[ListNode]
        :rtype: Optional[ListNode]
        """

        h = ListNode(None, None)
        nextNode = ListNode(None, None)

        h1 = nextNode
        mem = 0

        while l1 is not None or l2 is not None:
            h.next = nextNode
            h = h.next

            if l1 is None:
                tmp = l2.val + mem
                l2 = l2.next

            elif l2 is None:
                tmp = l1.val + mem
                l1 = l1.next

            else : 
                tmp = l1.val + l2.val + mem

                l1 = l1.next
                l2 = l2.next

            if tmp >= 10:
                mem = 1
                h.val = tmp % 10
            else:
                mem = 0
                h.val = tmp % 10

            nextNode = ListNode(None, None)

        if mem == 1:
            h.next = nextNode
            h = h.next
            h.val = 1

        return h1
```

# Submission
```
Runtime
6 ms
Beats
72.29%

Memory
12.66 MB
Beats
15.34%
```
