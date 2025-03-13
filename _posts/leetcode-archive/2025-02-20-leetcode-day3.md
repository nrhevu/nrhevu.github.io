---
title: "LeetCode - Day 3"
date: 2025-02-20
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Two Pointer
**Two Pointer** là một dạng bài khá phổ biến và cơ bản trong khi làm các bài giải thuật toán trên leetcode. Pattern này thường được áp dụng trong các bài tìm cặp số hoặc bộ số thỏa mãn 1 điều kiện gì đó. 

Ý tưởng khá là đơn giản, ta sẽ sử dụng 2 con trỏ: 1 con đặt ở đầu mảng, 1 con đặt ở cuối mảng. Ta sẽ đánh giá điều kiện cho cặp số mà 2 con trỏ đang trỏ đến và dịch con trả bên trái lên 1 bước nếu kết quả > điều kiện và ngược lại. Ta sẽ lặp lại bước trên đến khi tìm được cặp số thỏa mãn điều kiện hoặc khi 2 con trỏ đi qua nhau thì dừng thuật toán. 

Đây chỉ là ý tưởng cơ bản, sẽ có nhiều kiểu biến thể khác ví dụ như là sử dụng con trỏ nhanh chậm để tìm chu kỳ, v.v. Ta sẽ tìm hiểu phương pháp này qua 1 bài toán cơ bản sau đây.

![alt text](/assets/images/leetcode/2pointer.png)

# Problem
```
167. Two Sum II - Input Array Is Sorted
Solved
Medium
Topics
Companies
Given a 1-indexed array of integers numbers that is already sorted in non-decreasing order, find two numbers such that they add up to a specific target number. Let these two numbers be numbers[index1] and numbers[index2] where 1 <= index1 < index2 <= numbers.length.

Return the indices of the two numbers, index1 and index2, added by one as an integer array [index1, index2] of length 2.

The tests are generated such that there is exactly one solution. You may not use the same element twice.

Your solution must use only constant extra space.

 

Example 1:

Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore, index1 = 1, index2 = 2. We return [1, 2].
Example 2:

Input: numbers = [2,3,4], target = 6
Output: [1,3]
Explanation: The sum of 2 and 4 is 6. Therefore index1 = 1, index2 = 3. We return [1, 3].
Example 3:

Input: numbers = [-1,0], target = -1
Output: [1,2]
Explanation: The sum of -1 and 0 is -1. Therefore index1 = 1, index2 = 2. We return [1, 2].
 

Constraints:

2 <= numbers.length <= 3 * 104
-1000 <= numbers[i] <= 1000
numbers is sorted in non-decreasing order.
-1000 <= target <= 1000
The tests are generated such that there is exactly one solution.
```

# Solution 
``` python 
class Solution(object):
    def twoSum(self, numbers, target):
        """
        :type numbers: List[int]
        :type target: int
        :rtype: List[int]
        """
        
        p1 = 0 
        p2 = len(numbers) - 1

        while p1 < p2:
            s = numbers[p1] + numbers[p2]
            if s == target:
                return (p1+1, p2+1)
            elif s < target:
                p1 += 1
            else:
                p2 -= 1
```

# Submissions
![alt text](/assets/images/leetcode/167.%20Two%20Sum%20II%20-%20Input%20Array%20Is%20Sorted.png)
