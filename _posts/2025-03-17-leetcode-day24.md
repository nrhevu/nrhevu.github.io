---
title: "LeetCode - Day 24"
date: 2025-03-17
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
54. Spiral Matrix
Solved
Medium

Topics
Companies

Hint
Given an m x n matrix, return all elements of the matrix in spiral order.

 

Example 1:


Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]
Example 2:


Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
 

Constraints:

m == matrix.length
n == matrix[i].length
1 <= m, n <= 10
-100 <= matrix[i][j] <= 100
```

# Solution
```python
class Solution:
    def spiralOrder(self, matrix: List[List[int]]) -> List[int]:
        tr = 0
        br = len(matrix) - 1
        lc = 0
        rc = len(matrix[0]) - 1
        ans = []
        while tr <= br and lc <= rc:
            try:
                c = lc
                r = tr
                while c <= rc:
                    ans.append(matrix[tr][c])
                    c += 1
                tr += 1
                r = tr
                while r <= br:
                    ans.append(matrix[r][rc])
                    r += 1
                rc -= 1
                c = rc
                if tr <= br:
                    while c >= lc:
                        ans.append(matrix[br][c])
                        c -= 1
                    br -= 1
                r = br
                if lc <= rc:
                    while r >= tr:
                        ans.append(matrix[r][lc])
                        r -= 1
                    lc += 1
                c = lc
            except Exception as e:
                print(tr, br, lc, rc)
                print(r, c)
                raise e
        # if tr < br:
        #     for r in range(tr, br + 1):
        #         ans.append(matrix[r][lc])
        # if lc < rc:
        #     for c in range(lc, rc + 1):
        #         ans.append(matrix[tr][c])

        return ans
            
```

# Submission
```

Runtime

0
ms
Beats
100.00%
s
Memory
17.96
MB
Beats
18.81%
```