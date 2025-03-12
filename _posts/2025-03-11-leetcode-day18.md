---
title: "LeetCode - Day 18"
date: 2025-03-11
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```


Code





Testcase

Test Result
Test Result
21. Merge Two Sorted Lists
Solved
Easy

Topics
Companies
You are given the heads of two sorted linked lists list1 and list2.

Merge the two lists into one sorted list. The list should be made by splicing together the nodes of the first two lists.

Return the head of the merged linked list.

 

Example 1:


Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
Example 2:

Input: list1 = [], list2 = []
Output: []
Example 3:

Input: list1 = [], list2 = [0]
Output: [0]
 

Constraints:

The number of nodes in both lists is in the range [0, 50].
-100 <= Node.val <= 100
Both list1 and list2 are sorted in non-decreasing order.
```

# Solution
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def mergeTwoLists(self, list1: Optional[ListNode], list2: Optional[ListNode]) -> Optional[ListNode]:
        n1 = list1
        n2 = list2
        
        if n1 is None and n2 is None:
            return None
        elif n1 is None:
            return n2
        elif n2 is None:
            return n1
        else:
            if n1.val <= n2.val:
                head = n1
                n1 = n1.next
            else:
                head = n2
                n2 = n2.next

        n = head
        
        while n1 is not None or n2 is not None:
            try:
                if n1.val <= n2.val:
                    n.next = n1
                    n = n.next
                    n1 = n1.next
                else:
                    n.next = n2
                    n = n.next
                    n2 = n2.next
            except:
                if n1 is None:
                    n.next = n2
                    n = n.next
                    n2 = n2.next
                    continue
                elif n2 is None:
                    n.next = n1
                    n = n.next
                    n1 = n1.next
                    continue

        return head
```

# Submission
```

Runtime

0
ms
Beats
100.00%


Memory
17.84
MB
Beats
38.59%
```