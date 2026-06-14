# 🔲 Matrix Problems — Complete DSA Guide

> Transposition, Rotation, Spiral, Row/Column operations, Binary Search in Matrix

---

## 📌 Matrix Basics

```
Matrix (m x n):
     col→  0    1    2
row↓  0  [ 1,   2,   3 ]
      1  [ 4,   5,   6 ]
      2  [ 7,   8,   9 ]

Element at row i, col j → matrix[i][j]
```

---

## 🔧 Part 1 — Matrix Transformations

---

### 🔷 1. Transpose a Matrix

**Transpose:** Swap rows and columns → matrix[i][j] ↔ matrix[j][i]

```
Input:          Output:
1 2 3           1 4 7
4 5 6    →      2 5 8
7 8 9           3 6 9
```

```python
def transpose(matrix):
    n = len(matrix)
    for i in range(n):
        for j in range(i + 1, n):      # only upper triangle
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    return matrix

# Time: O(n²)  Space: O(1) in-place
```

```java
void transpose(int[][] matrix) {
    int n = matrix.length;
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
}
```

---

### 🔷 2. Rotate Matrix 90° Clockwise

```
1 2 3        7 4 1
4 5 6   →    8 5 2
7 8 9        9 6 3
```

**Formula:** Rotate 90° = Transpose + Reverse each row

```python
def rotate_90_clockwise(matrix):
    # Step 1: Transpose
    n = len(matrix)
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]

    # Step 2: Reverse each row
    for row in matrix:
        row.reverse()

    return matrix

# Time: O(n²)  Space: O(1)
```

---

### 🔷 3. Set Matrix Zeros (Row & Column to 0)

**Problem:** If matrix[i][j] == 0, set entire row i and col j to 0.

```
Input:          Output:
1 1 1           1 0 1
1 0 1    →      0 0 0
1 1 1           1 0 1
```

```python
def set_zeroes(matrix):
    m, n = len(matrix), len(matrix[0])
    zero_rows = set()
    zero_cols = set()

    # Pass 1: find zeros
    for i in range(m):
        for j in range(n):
            if matrix[i][j] == 0:
                zero_rows.add(i)
                zero_cols.add(j)

    # Pass 2: set zeros
    for i in range(m):
        for j in range(n):
            if i in zero_rows or j in zero_cols:
                matrix[i][j] = 0

# Time: O(m*n)  Space: O(m+n)
```

**O(1) Space version:**

```python
def set_zeroes_o1(matrix):
    m, n = len(matrix), len(matrix[0])
    first_row_zero = any(matrix[0][j] == 0 for j in range(n))
    first_col_zero = any(matrix[i][0] == 0 for i in range(m))

    # Use first row/col as markers
    for i in range(1, m):
        for j in range(1, n):
            if matrix[i][j] == 0:
                matrix[i][0] = 0
                matrix[0][j] = 0

    for i in range(1, m):
        for j in range(1, n):
            if matrix[i][0] == 0 or matrix[0][j] == 0:
                matrix[i][j] = 0

    if first_row_zero:
        for j in range(n): matrix[0][j] = 0
    if first_col_zero:
        for i in range(m): matrix[i][0] = 0

# Time: O(m*n)  Space: O(1)
```

---

### 🔷 4. Spiral Order Print

**Problem:** Print matrix elements in spiral order.

```
Input:              Output:
1  2  3             [1, 2, 3, 6, 9, 8, 7, 4, 5]
4  5  6
7  8  9
```

```python
def spiral_order(matrix):
    result = []
    if not matrix:
        return result

    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1

    while top <= bottom and left <= right:
        # → go right
        for col in range(left, right + 1):
            result.append(matrix[top][col])
        top += 1

        # ↓ go down
        for row in range(top, bottom + 1):
            result.append(matrix[row][right])
        right -= 1

        # ← go left
        if top <= bottom:
            for col in range(right, left - 1, -1):
                result.append(matrix[bottom][col])
            bottom -= 1

        # ↑ go up
        if left <= right:
            for row in range(bottom, top - 1, -1):
                result.append(matrix[row][left])
            left += 1

    return result

# Time: O(m*n)  Space: O(1)
```

---

### 🔷 5. Diagonal Traversal

```python
def find_diagonal_order(matrix):
    m, n = len(matrix), len(matrix[0])
    result = []
    going_up = True

    r, c = 0, 0
    for _ in range(m * n):
        result.append(matrix[r][c])

        if going_up:
            if c == n - 1:
                r += 1; going_up = False
            elif r == 0:
                c += 1; going_up = False
            else:
                r -= 1; c += 1
        else:
            if r == m - 1:
                c += 1; going_up = True
            elif c == 0:
                r += 1; going_up = True
            else:
                r += 1; c -= 1

    return result

# Time: O(m*n)  Space: O(1)
```

---

## 🔧 Part 2 — Binary Search in Matrix

---

### 🔷 6. Search in Row-wise & Column-wise Sorted Matrix

**Problem:** Each row and column is sorted. Find target.

```
Matrix:
 1  4  7 11 15
 2  5  8 12 19
 3  6  9 16 22
10 13 14 17 24
18 21 23 26 30

Target: 5 → Found at (1,1)
```

```python
def search_matrix_ii(matrix, target):
    if not matrix:
        return False

    row = 0
    col = len(matrix[0]) - 1    # Start from top-right corner

    while row < len(matrix) and col >= 0:
        if matrix[row][col] == target:
            return True
        elif matrix[row][col] > target:
            col -= 1     # go left (smaller values)
        else:
            row += 1     # go down (larger values)

    return False

# Time: O(m + n)  Space: O(1)
```

---

### 🔷 7. Search in Fully Sorted Matrix (Binary Search)

**Problem:** Matrix where each row is sorted and first element of next row > last of prev row.

```
Matrix:
 1  3  5  7
10 11 16 20
23 30 34 60

Target: 3 → True
```

```python
def search_matrix(matrix, target):
    m, n = len(matrix), len(matrix[0])
    low, high = 0, m * n - 1

    while low <= high:
        mid = (low + high) // 2
        # Convert 1D mid index → 2D row, col
        row, col = mid // n, mid % n
        val = matrix[row][col]

        if val == target:
            return True
        elif val < target:
            low = mid + 1
        else:
            high = mid - 1

    return False

# Time: O(log(m*n))  Space: O(1)
```

---

### 🔷 8. Find Peak Element in Matrix

```python
def find_peak_grid(matrix):
    m, n = len(matrix), len(matrix[0])
    lo, hi = 0, n - 1

    while lo <= hi:
        mid_col = (lo + hi) // 2
        # Find max element in mid column
        max_row = max(range(m), key=lambda r: matrix[r][mid_col])

        left  = matrix[max_row][mid_col - 1] if mid_col > 0 else -1
        right = matrix[max_row][mid_col + 1] if mid_col < n-1 else -1

        if matrix[max_row][mid_col] > left and matrix[max_row][mid_col] > right:
            return [max_row, mid_col]
        elif left > matrix[max_row][mid_col]:
            hi = mid_col - 1
        else:
            lo = mid_col + 1

    return [-1, -1]

# Time: O(m log n)  Space: O(1)
```

---

### 🔷 9. Count Negative Numbers in Sorted Matrix

```python
def count_negatives(grid):
    m, n = len(grid), len(grid[0])
    count = 0
    row, col = 0, n - 1   # start from top-right

    while row < m and col >= 0:
        if grid[row][col] < 0:
            count += m - row   # all elements below are also negative
            col -= 1
        else:
            row += 1

    return count

# Time: O(m + n)  Space: O(1)
```

---

### 🔷 10. Word Search in Matrix (DFS)

**Problem:** Find if word exists in matrix using adjacent cells.

```python
def exist(board, word):
    m, n = len(board), len(board[0])

    def dfs(r, c, idx):
        if idx == len(word):
            return True
        if r < 0 or r >= m or c < 0 or c >= n:
            return False
        if board[r][c] != word[idx]:
            return False

        temp = board[r][c]
        board[r][c] = '#'    # mark visited

        found = (dfs(r+1, c, idx+1) or dfs(r-1, c, idx+1) or
                 dfs(r, c+1, idx+1) or dfs(r, c-1, idx+1))

        board[r][c] = temp   # restore
        return found

    for i in range(m):
        for j in range(n):
            if dfs(i, j, 0):
                return True

    return False

# Time: O(m*n*4^L)  L = word length
```

---

## 🗺️ Problem → Algorithm Map

| Problem | Algorithm | Time |
|---------|-----------|------|
| Transpose | Swap upper triangle | O(n²) |
| Rotate 90° | Transpose + reverse rows | O(n²) |
| Set zeros | Mark first row/col | O(m*n) |
| Spiral print | Shrink boundaries | O(m*n) |
| Search (row+col sorted) | Start top-right | O(m+n) |
| Search (fully sorted) | Binary Search (1D) | O(log mn) |
| Count negatives | Start top-right | O(m+n) |
| Word search | DFS + backtrack | O(m*n*4^L) |

---

## ⏱️ Complexity Summary

| Operation | Time | Space |
|-----------|------|-------|
| Traverse full matrix | O(m*n) | O(1) |
| Binary search (fully sorted) | O(log(m*n)) | O(1) |
| Search (row+col sorted) | O(m+n) | O(1) |
| Spiral / Diagonal | O(m*n) | O(1) |
| Rotate / Transpose | O(n²) | O(1) |
