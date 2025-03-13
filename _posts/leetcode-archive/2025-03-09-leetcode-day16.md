---
title: "LeetCode - Day 16"
date: 2025-03-09
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
42. Trapping Rain Water
Solved
Hard

Topics
Companies
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it can trap after raining.

 

Example 1:


Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.
Example 2:

Input: height = [4,2,0,3,2,5]
Output: 9
 

Constraints:

n == height.length
1 <= n <= 2 * 104
0 <= height[i] <= 105
```

# Solution
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        total_trap = 0
        highest = 0

        for h in height:
            if h > highest:
                highest = h
            else:
                total_trap += highest - h

        highest2 = height[-1]
        for h in height[::-1]:
            if h == highest:
                break
            if h > highest2:
                highest2 = h
            
            total_trap -= highest - highest2

        return total_trap
```

# Submission
```
Runtime
7 ms
Beats
89.46%

Memory
19.26 MB
Beats
69.93%
```