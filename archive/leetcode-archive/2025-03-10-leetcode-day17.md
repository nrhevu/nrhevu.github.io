---
title: "LeetCode - Day 17"
date: 2025-03-10
categories: [LeetCode]
tags: "LeetCode"
author: Nguyen The Vu
math: true
---

# Problem
```
304. Range Sum Query 2D - Immutable
Solved
Medium

Topics
Companies
Given a 2D matrix matrix, handle multiple queries of the following type:

Calculate the sum of the elements of matrix inside the rectangle defined by its upper left corner (row1, col1) and lower right corner (row2, col2).
Implement the NumMatrix class:

NumMatrix(int[][] matrix) Initializes the object with the integer matrix matrix.
int sumRegion(int row1, int col1, int row2, int col2) Returns the sum of the elements of matrix inside the rectangle defined by its upper left corner (row1, col1) and lower right corner (row2, col2).
You must design an algorithm where sumRegion works on O(1) time complexity.

 

Example 1:


Input
["NumMatrix", "sumRegion", "sumRegion", "sumRegion"]
[[[[3, 0, 1, 4, 2], [5, 6, 3, 2, 1], [1, 2, 0, 1, 5], [4, 1, 0, 1, 7], [1, 0, 3, 0, 5]]], [2, 1, 4, 3], [1, 1, 2, 2], [1, 2, 2, 4]]
Output
[null, 8, 11, 12]

Explanation
NumMatrix numMatrix = new NumMatrix([[3, 0, 1, 4, 2], [5, 6, 3, 2, 1], [1, 2, 0, 1, 5], [4, 1, 0, 1, 7], [1, 0, 3, 0, 5]]);
numMatrix.sumRegion(2, 1, 4, 3); // return 8 (i.e sum of the red rectangle)
numMatrix.sumRegion(1, 1, 2, 2); // return 11 (i.e sum of the green rectangle)
numMatrix.sumRegion(1, 2, 2, 4); // return 12 (i.e sum of the blue rectangle)
 

Constraints:

m == matrix.length
n == matrix[i].length
1 <= m, n <= 200
-104 <= matrix[i][j] <= 104
0 <= row1 <= row2 < m
0 <= col1 <= col2 < n
At most 104 calls will be made to sumRegion.
```

# Solution
*2 giải pháp dưới đây cùng 1 thuật toán nhưng với 2 cách code khác nhau, cùng xem và so sánh nhé*

## Solution1
```python
import numpy as np

class NumMatrix:

    def __init__(self, matrix: List[List[int]]):
        self.n, self.m = len(matrix), len(matrix[0])
        self.prefix_sum = np.zeros((self.n, self.m), dtype=int)

        for i in range(self.n):
            for j in range(self.m):
                self.prefix_sum[i][j] = matrix[i][j]
                if i > 0:
                    self.prefix_sum[i][j] += self.prefix_sum[i-1][j]
                if j > 0:
                    self.prefix_sum[i][j] += self.prefix_sum[i][j-1]
                if i > 0 and j > 0:
                    self.prefix_sum[i][j] -= self.prefix_sum[i-1][j-1]

    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        total = self.prefix_sum[row2][col2]
        if row1 > 0:
            total -= self.prefix_sum[row1 - 1][col2]
        if col1 > 0:
            total -= self.prefix_sum[row2][col1 - 1]
        if row1 > 0 and col1 > 0:
            total += self.prefix_sum[row1 - 1][col1 - 1]

        return int(total)


# Your NumMatrix object will be instantiated and called as such:
# obj = NumMatrix(matrix)
# param_1 = obj.sumRegion(row1,col1,row2,col2)
```

## Solution2
```python
class NumMatrix:

    def __init__(self, matrix: List[List[int]]):
        n, m = len(matrix), len(matrix[0])

        for i in range(1, n):
            matrix[i][0] += matrix[i-1][0]

        for i in range(1, m):
            matrix[0][i] += matrix[0][i-1]

        for i in range(1, n):
            for j in range(1, m):
                matrix[i][j] += matrix[i-1][j] + matrix[i][j-1] - matrix[i-1][j-1]

        self.prefix_sum = matrix

    def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        if row1 > 0 and col1 > 0:
            return self.prefix_sum[row2][col2] - self.prefix_sum[row1 - 1][col2] - self.prefix_sum[row2][col1 - 1] + self.prefix_sum[row1 - 1][col1 - 1]
        elif row1 > 0:
            return self.prefix_sum[row2][col2] - self.prefix_sum[row1 - 1][col2]
        elif col1 > 0:
            return self.prefix_sum[row2][col2] - self.prefix_sum[row2][col1 - 1]
        else:
            return self.prefix_sum[row2][col2]


# Your NumMatrix object will be instantiated and called as such:
# obj = NumMatrix(matrix)
# param_1 = obj.sumRegion(row1,col1,row2,col2)
```

# Submission
## Solution1
```
Runtime
875 ms
Beats
12.34%

Memory
41.02 MB
Beats
5.01%
```

## Solution2
```
Runtime
87 ms
Beats
93.49%

Memory
29.60 MB
Beats
94.28%
```

Dễ thấy giải pháp 1 thừa mất 1 biến prefix_sum khởi tạo bằng numpy trong khi có thể lưu cùng với biến matrix luôn. Giải pháp 1 cũng thừa khá nhiều lần if else dẫn đến phải truy cập vào mảng nhiều hơn mức cần thiết nên code bị chậm. Ở giải pháp 2, code đã được tối ưu chỉ bằng cách bỏ đi các thư viện thừa, bỏ đi các biến thừa và bỏ đi những thao tác không cần thiết và đã cải thiện 10 lần tốc độ và 10MB bộ nhớ. 