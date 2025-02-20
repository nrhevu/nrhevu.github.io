---
title: "LeetCode - Day 2"
date: 2025-02-19
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
15. 3Sum (Medium)
Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.

Notice that the solution set must not contain duplicate triplets.

 

Example 1:

Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explanation: 
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0.
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0.
The distinct triplets are [-1,0,1] and [-1,-1,2].
Notice that the order of the output and the order of the triplets does not matter.
Example 2:

Input: nums = [0,1,1]
Output: []
Explanation: The only possible triplet does not sum up to 0.
Example 3:

Input: nums = [0,0,0]
Output: [[0,0,0]]
Explanation: The only possible triplet sums up to 0.
 

Constraints:

3 <= nums.length <= 3000
-105 <= nums[i] <= 105
```

# Solution1
```python
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        nums = [(n, i) for i, n in enumerate(nums)]

        tmp = set()
        nums = sorted(nums, key=lambda x: x[0])
        for p3 in range(1, len(nums) - 1):
            p1 = 0
            p2 = len(nums) - 1
            while p1 != p3 and p2 != p3:
                sum_all = nums[p1][0] + nums[p2][0] + nums[p3][0]
                if sum_all == 0:
                    tmp.add(tuple(sorted((nums[p1][0], nums[p2][0], nums[p3][0]))))
                    p1 += 1
                elif sum_all > 0:
                    p2 -= 1
                else:
                    p1 += 1

        # result = []
        # for r in tmp:
        #     result.append()

        return list(tmp)
```

# Submission1
![alt text](/assets/images/leetcode/15-3sum-1.png)

# Solution2
```python
class Solution(object):
    def threeSum(self, nums):
        """
        :type nums: List[int]
        :rtype: List[List[int]]
        """
        result = []
        nums = sorted(nums)
        n = len(nums)

        for p1 in range(n - 2):
            p2 = p1 + 1
            p3 = n - 1

            if nums[p1] > 0:
                break
            if p1 > 0 and nums[p1] == nums[p1-1]:
                continue # pass if duplicate answer

            while p2 < p3:
                s = nums[p1] + nums[p2] + nums[p3]
                if s == 0:
                    result.append((nums[p1], nums[p2], nums[p3]))

                    while p2 < p3 and nums[p2] == nums[p2+1]:
                        p2 += 1
                    while p2 < p3 and nums[p3] == nums[p3-1]:
                        p3 -= 1 # avoid duplicate 

                    p2 += 1
                    p3 -= 1
                elif s < 0:
                    p2 += 1
                else:
                    p3 -= 1

        return result
```

# Submission2
![alt text](/assets/images/leetcode/15-3sum-2.png)