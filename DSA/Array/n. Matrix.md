# Module 14: Matrix (2D Arrays)

## Definition

A **Matrix (2D Array)** is a data structure that stores elements in a grid of rows and columns. Each element is accessed using two indices:

* Row index
* Column index

It is widely used in problems involving grids, graphs, images, and dynamic programming.

---

# 1. Matrix Traversal

Visiting every element of the matrix exactly once using nested loops.

### Idea:

Use two loops:

* Outer loop → rows
* Inner loop → columns

### Time Complexity:

O(m × n)

---

# 2. Row-wise Traversal

Traverse each row from left to right.

Example:

```text id="r1"
1 2 3
4 5 6
```

Output:

```text id="r2"
1 2 3 4 5 6
```

### Idea:

Fix row, move column.

---

# 3. Column-wise Traversal

Traverse each column from top to bottom.

Output:

```text id="c1"
1 4 2 5 3 6
```

### Idea:

Fix column, move row.

---

# 4. Diagonal Traversal

Traverse elements along diagonals of the matrix.

Used in:

* Matrix pattern problems
* DP optimizations

### Types:

* Primary diagonal (top-left to bottom-right)
* Secondary diagonal (top-right to bottom-left)

---

# 5. Spiral Traversal

Traverse matrix in spiral order:

Order:
Right → Down → Left → Up (repeat)

### Example:

```text id="s1"
1  2  3
4  5  6
7  8  9
```

Output:

```text id="s2"
1 2 3 6 9 8 7 4 5
```

---

# 6. Wave Traversal

Traverse columns in alternating direction:

* Column 0 → top to bottom
* Column 1 → bottom to top
* Column 2 → top to bottom

### Idea:

Alternate direction per column.

---

# 7. Matrix Rotation

Rotate matrix by 90°, 180°, or 270°.

### 90° Clockwise Rotation Steps:

1. Transpose matrix
2. Reverse each row

### Time Complexity:

O(m × n)

---

# 8. Matrix Transpose

Transpose means swapping rows and columns.

Formula:

```text id="t1"
A[i][j] → A[j][i]
```

### Example:

```text id="t2"
1 2 3      1 4 7
4 5 6  →   2 5 8
7 8 9      3 6 9
```

### Time Complexity:

O(m × n)

---

# Complexity Summary

| Operation             | Time Complexity |
| --------------------- | --------------- |
| Matrix Traversal      | O(m × n)        |
| Row-wise Traversal    | O(m × n)        |
| Column-wise Traversal | O(m × n)        |
| Diagonal Traversal    | O(m × n)        |
| Spiral Traversal      | O(m × n)        |
| Wave Traversal        | O(m × n)        |
| Transpose             | O(m × n)        |
| Rotation              | O(m × n)        |

---

# Key Idea

Matrix problems are solved using:

* Nested loops
* Direction control (right, left, up, down)
* Boundary management
* Index manipulation

---

# Applications

* Image processing
* Game boards (chess, sudoku)
* Graph representation (adjacency matrix)
* Dynamic programming grids
* Pathfinding problems

---

# Advantages

* Simple structure for grid-based problems
* Efficient for structured data
* Direct indexing access
* Used in many real-world applications

---

# Limitations

* High memory usage for large matrices
* Complex traversal logic (spiral, diagonal)
* Inefficient for sparse data

---

# Interview Questions

## 1. What is a matrix?

**Answer:**
A matrix is a 2D array arranged in rows and columns where each element is accessed using two indices.

---

## 2. What is matrix traversal?

**Answer:**
Matrix traversal is visiting every element of the matrix using nested loops.

---

## 3. What is row-wise traversal?

**Answer:**
Traversing a matrix row by row from left to right.

---

## 4. What is column-wise traversal?

**Answer:**
Traversing a matrix column by column from top to bottom.

---

## 5. What is spiral traversal?

**Answer:**
Traversal of a matrix in spiral order starting from the outer layer moving inward.

---

## 6. How do you rotate a matrix by 90°?

**Answer:**
First transpose the matrix, then reverse each row.

---

## 7. What is transpose of a matrix?

**Answer:**
Swapping rows and columns of a matrix.

---

## 8. What is the time complexity of matrix operations?

**Answer:**
Most matrix operations take **O(m × n)** time.

---

## 9. What is wave traversal?

**Answer:**
Traversing matrix columns in alternating directions (top-down, bottom-up).

---

## 10. Where are matrix problems used?

**Answer:**
They are used in graphics, DP, games, image processing, and grid-based problems.
