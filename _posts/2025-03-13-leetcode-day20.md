---
title: "LeetCode - Day 20"
date: 2025-03-13
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
79. Word Search
Solved
Medium

Topics
Companies
Given an m x n grid of characters board and a string word, return true if word exists in the grid.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.

 

Example 1:


Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
Output: true
Example 2:


Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
Output: true
Example 3:


Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
Output: false
 

Constraints:

m == board.length
n = board[i].length
1 <= m, n <= 6
1 <= word.length <= 15
board and word consists of only lowercase and uppercase English letters.
 

Follow up: Could you use search pruning to make your solution faster with a larger board?
```

# Solution
```python
class Solution(object):
    def exist(self, board, word):
        """
        :type board: List[List[str]]
        :type word: str
        :rtype: bool
        """

        def findnext(board, word, pos, i, j):
            if board[i][j] == word[pos]:
                if pos == len(word) - 1:
                    return True
                board[i][j] = ""

                i_m1 = max(i-1, 0)
                i_p1 = min(i+1, n)
                j_m1 = max(j-1, 0)
                j_p1 = min(j+1, m)
                n_pos = pos + 1
                
                re = findnext(board, word, n_pos, i_m1, j) or findnext(board, word, n_pos, i, j_p1) or findnext(board, word, n_pos, i_p1, j) or findnext(board, word, n_pos, i, j_m1)
                    
                board[i][j] = word[pos]

                return re
            else:
                return False

        n = len(board) - 1 
        m = len(board[0]) - 1
        lw = len(word)

        for i in range(n+1):
            for j in range(m+1):
                if findnext(board, word, 0, i, j):
                    return True

        return False
        
        

```

# Submission
```

Runtime
4111 ms
Beats
62.04%


Memory
18.08 MB
Beats
21.55%
```