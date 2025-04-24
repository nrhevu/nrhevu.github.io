---
title: "LeetCode - Day 19"
date: 2025-03-12
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
61. Rotate List
Solved
Medium

Topics
Companies
Given the head of a linked list, rotate the list to the right by k places.

 

Example 1:


Input: head = [1,2,3,4,5], k = 2
Output: [4,5,1,2,3]
Example 2:


Input: head = [0,1,2], k = 4
Output: [2,0,1]
 

Constraints:

The number of nodes in the list is in the range [0, 500].
-100 <= Node.val <= 100
0 <= k <= 2 * 109
```

# Solution
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def rotateRight(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if head is None:
            return head
        length = 1
        curr = head

        while curr.next is not None:
            curr = curr.next
            length += 1
        pos = length - k % length

        curr.next = head
        curr = head
        for _ in range(pos-1):
            curr = curr.next

        head = curr.next
        curr.next = None
        return head
```

# Submission
```
Runtime
0 ms
Beats
100.00%

Memory
17.78 MB
Beats
85.56% 
```