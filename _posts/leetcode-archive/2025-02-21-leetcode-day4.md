---
title: "LeetCode - Day 4"
date: 2025-02-21
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
45. Jump Game II
Solved
Medium
Topics
Companies
You are given a 0-indexed array of integers nums of length n. You are initially positioned at nums[0].

Each element nums[i] represents the maximum length of a forward jump from index i. In other words, if you are at nums[i], you can jump to any nums[i + j] where:

0 <= j <= nums[i] and
i + j < n
Return the minimum number of jumps to reach nums[n - 1]. The test cases are generated such that you can reach nums[n - 1].

 

Example 1:

Input: nums = [2,3,1,1,4]
Output: 2
Explanation: The minimum number of jumps to reach the last index is 2. Jump 1 step from index 0 to 1, then 3 steps to the last index.
Example 2:

Input: nums = [2,3,0,1,4]
Output: 2
 

Constraints:

1 <= nums.length <= 104
0 <= nums[i] <= 1000
It's guaranteed that you can reach nums[n - 1].
```

# Solution 1
```python
class Solution(object):
    def jump(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        
        n = len(nums)
        tmp = [0] * n
        if n == 1:
            return 0
        for i in range(n - 1, -1, -1):
            if i + nums[i] >= n - 1:
                tmp[i] = 1

            else:
                if nums[i] == 0:
                    tmp[i] = 999999
                else:
                    tmp[i] = min(tmp[i+1:i+1+nums[i]]) + 1

        return tmp[0]
```

# Submission 1
```
Runtime
563 ms Beats 21.11%
Memory 
13.34 MB Beats 20.14%
```

# Solution 2
```python
class Solution(object):
    def jump(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        n = len(nums)
        
        jump = 0
        current_end = 0
        fartest = 0

        for i in range(n - 1):
            fartest = max(fartest, i + nums[i])
            if i == current_end:
                jump += 1
                current_end = fartest

        return jump
```

# Submission 2
```
Runtime
7 ms Beats 80%
Memory 
13.26 MB Beats 45.14%
```