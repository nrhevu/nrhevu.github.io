---
title: "LeetCode - Day 23"
date: 2025-03-16
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
3. Longest Substring Without Repeating Characters
Solved
Medium

Topics
Companies

Hint
Given a string s, find the length of the longest substring without duplicate characters.

 

Example 1:

Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
Example 2:

Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
Example 3:

Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
 

Constraints:

0 <= s.length <= 5 * 104
s consists of English letters, digits, symbols and spaces.
```

# Solution1
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if len(s) == 0:
            return 0
        elif len(s) == 1:
            return 1
        
        chars = set([s[0]])
        longest = 1
        left = 0
        for right in range(1, len(s)):
            while s[right] in chars and left < right:
                chars.remove(s[left])
                left += 1
            chars.add(s[right])
            if right-left + 1 > longest:
                longest = right-left + 1
                    
        return longest
            
```

# Solution2
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        seen = set()
        l = 0
        longest = 0

        for r in range(len(s)):
            while s[r] in seen:
                seen.remove(s[l])
                l += 1

            seen.add(s[r])
            longest = max(longest, r - l + 1)

        return longest
```

# Solution3
Chỉ cắt tỉa đi 1 lần lặp từ solution2 và giải quyết các case dễ ngay từ đầu
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        if len(s) == 0:
            return 0
        elif len(s) == 1:
            return 1

        seen = set(s[0])
        l = 0
        longest = 0

        for r in range(1, len(s)):
            while s[r] in seen:
                seen.remove(s[l])
                l += 1

            seen.add(s[r])
            longest = max(longest, r - l + 1)

        return longest
```

# Submission1
```

Runtime

21
ms
Beats
37.62%

Memory
18.08
MB
Beats
12.70%
```

# Submission2
```

Runtime

23
ms
Beats
32.69%

Memory
18.08
MB
Beats
12.70%
```

# Submission3
```

Runtime

11
ms
Beats
95.40%


Memory
17.92
MB
Beats
34.41%
```