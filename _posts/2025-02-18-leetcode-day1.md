---
title: "LeetCode - Day 1"
date: 2025-02-18
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problems:

```
238.   Product of Array Except Self
Medium

Given an integer array nums, return an array answer such that answer[i] is equal to the product of all the elements of nums except nums[i].

The product of any prefix or suffix of nums is guaranteed to fit in a 32-bit integer.

You must write an algorithm that runs in O(n) time and without using the division operation.

Example 1:

Input: nums = [1,2,3,4]
Output: [24,12,8,6]
Example 2:

Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
 

Constraints:

2 <= nums.length <= 105
-30 <= nums[i] <= 30
The input is generated such that answer[i] is guaranteed to fit in a 32-bit integer.
 

Follow up: Can you solve the problem in O(1) extra space complexity? (The output array does not count as extra space for space complexity analysis.)
```

# Solution: 
``` python
class Solution(object):
    def productExceptSelf(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        
        result = [1] * len(nums)
        current_product = 1
        for i in range(len(nums) - 1, 0, -1):
            current_product *= nums[i]
            result[i-1] = current_product

        current_product = 1
        for i in range(0, len(nums)):
            result[i] *= current_product
            current_product *= nums[i]

        return result
```

# Submission
![alt text](/assets/images/leetcode/238-submission.png)