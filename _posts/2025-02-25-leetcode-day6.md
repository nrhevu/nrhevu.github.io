---
title: "LeetCode - Day 6"
date: 2025-02-24
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
57. Insert Interval
Solved
Medium

Topics
Companies

Hint
You are given an array of non-overlapping intervals intervals where intervals[i] = [starti, endi] represent the start and the end of the ith interval and intervals is sorted in ascending order by starti. You are also given an interval newInterval = [start, end] that represents the start and end of another interval.

Insert newInterval into intervals such that intervals is still sorted in ascending order by starti and intervals still does not have any overlapping intervals (merge overlapping intervals if necessary).

Return intervals after the insertion.

Note that you don't need to modify intervals in-place. You can make a new array and return it.

 

Example 1:

Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
Example 2:

Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
 

Constraints:

0 <= intervals.length <= 104
intervals[i].length == 2
0 <= starti <= endi <= 105
intervals is sorted by starti in ascending order.
newInterval.length == 2
0 <= start <= end <= 105
```

# Solution 
```python 
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:    
        if len(intervals) == 0:
            return [newInterval]

        # binary search to determine appropriate position
        start = 0
        end = len(intervals) - 1
        
        while start <= end:
            mid = start + (end - start) // 2
            if intervals[mid][0] == newInterval[0]:
                loc = mid
                break
            elif intervals[mid][0] > newInterval[0]:
                end = mid - 1
            else:
                start = mid + 1

            loc = start

        start = newInterval[0]
        end = newInterval[1]

        # find interval
        i = loc - 1
        while i >= 0 and intervals[i][1] >= start:
            start = min(intervals[i][0], start)
            end = max(intervals[i][1], end)
            i -= 1

        j = loc
        while j < len(intervals) and intervals[j][0] <= end:
            start = min(intervals[j][0], start)
            end = max(intervals[j][1], end)
            j += 1

        return_intervals = intervals[:i+1] + [[start, end]] + intervals[j:]

        return return_intervals
```