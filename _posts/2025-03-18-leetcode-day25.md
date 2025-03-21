---
title: "LeetCode - Day 25"
date: 2025-03-15
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---


# Problem
```
92. Reverse Linked List II
Attempted
Medium

Topics
Companies
Given the head of a singly linked list and two integers left and right where left <= right, reverse the nodes of the list from position left to position right, and return the reversed list.

 

Example 1:


Input: head = [1,2,3,4,5], left = 2, right = 4
Output: [1,4,3,2,5]
Example 2:

Input: head = [5], left = 1, right = 1
Output: [5]
 

Constraints:

The number of nodes in the list is n.
1 <= n <= 500
-500 <= Node.val <= 500
1 <= left <= right <= n

```

# Solution
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseBetween(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        if not head or left == right:
            return head
        
        dummy = ListNode(0)
        dummy.next = head
        prev = dummy
        
        for _ in range(left - 1):
            prev = prev.next
        
        reverse_prev = prev
        current = prev.next
        next_node = None
        
        for _ in range(right - left + 1):
            temp = current.next
            current.next = next_node
            next_node = current
            current = temp
        
        reverse_prev.next.next = current
        reverse_prev.next = next_node
        
        return dummy.next
            

        
            
```

# Submission
```

Runtime

0
ms
Beats
100.00%

Memory
17.89
MB
Beats
57.27%

```
