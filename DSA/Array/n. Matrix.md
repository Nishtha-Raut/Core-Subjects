# Matrix (2D Arrays Basics)

## Definition

A **Matrix** is a **2D array** arranged in rows and columns. It is used to store data in tabular form.

A matrix element is accessed using two indices:

* Row index
* Column index

Example:

```text id="m1"
[ [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9] ]
```

---

# Representation

A matrix of size `m × n` means:

* m rows
* n columns

Example:

```text id="m2"
3 × 3 matrix
```

---

# Types of Matrix

## 1. Row Matrix

Only one row.

## 2. Column Matrix

Only one column.

## 3. Square Matrix

Same number of rows and columns.

## 4. Diagonal Matrix

Non-diagonal elements are 0.

## 5. Identity Matrix

Diagonal elements are 1.

---

# Basic Operations on Matrix

## 1. Traversal

Visiting each element using nested loops.

Time Complexity:

```text id="t1"
O(m × n)
```

---

## 2. Addition

Add corresponding elements of two matrices.

Condition:
Same dimensions required.

Time Complexity:

```text id="t2"
O(m × n)
```

---

## 3. Subtraction

Same as addition but subtract elements.

Time Complexity:

```text id="t3"
O(m × n)
```

---

## 4. Multiplication

Multiply rows of first matrix with columns of second matrix.

Condition:
Columns of first = Rows of second

Time Complexity:

```text id="t4"
O(n³) (basic approach)
```

---

## 5. Transpose

Swap rows and columns.

Example:

```text id="m3"
[1 2 3]      [1 4 7]
[4 5 6]  →   [2 5 8]
[7 8 9]      [3 6 9]
```

Time Complexity:

```text id="t5"
O(m × n)
```

---

# Key Idea

Matrix problems are solved using:

* Nested loops
* Row-wise or column-wise traversal
* Index manipulation

---

# Applications

* Image processing
* Graph representation (adjacency matrix)
* Dynamic programming (grid problems)
* Machine learning datasets
* Game boards (chess, sudoku)

---

# Advantages

* Easy representation of structured data
* Efficient for grid-based problems
* Supports mathematical operations
* Used in real-world systems

---

# Limitations

* High space usage for sparse data
* Complex multiplication logic
* Fixed-size structure in static arrays
* Inefficient for large sparse matrices

---

# Complexity Analysis

| Operation      | Time Complexity |
| -------------- | --------------- |
| Traversal      | O(m × n)        |
| Addition       | O(m × n)        |
| Subtraction    | O(m × n)        |
| Multiplication | O(n³)           |
| Transpose      | O(m × n)        |

---

# Interview Questions

## 1. What is a matrix?

**Answer:**
A matrix is a 2D array arranged in rows and columns used to store data in tabular form.

---

## 2. How do you access elements in a matrix?

**Answer:**
Using row and column indices:

```text
matrix[i][j]
```

---

## 3. What is the time complexity of matrix traversal?

**Answer:**
O(m × n), where m is rows and n is columns.

---

## 4. What is a square matrix?

**Answer:**
A matrix with equal number of rows and columns.

---

## 5. What is transpose of a matrix?

**Answer:**
Swapping rows and columns of a matrix.

---

## 6. What is the condition for matrix multiplication?

**Answer:**
Number of columns in first matrix must equal number of rows in second matrix.

---

## 7. Why is matrix multiplication O(n³)?

**Answer:**
Because each element requires nested iteration over rows and columns.

---

## 8. Where are matrices used?

**Answer:**

* Graphics
* AI/ML
* Graphs
* Games
* Grid-based problems

---

## 9. What is identity matrix?

**Answer:**
A square matrix with 1s on diagonal and 0s elsewhere.

---

## 10. What is diagonal matrix?

**Answer:**
A matrix where all non-diagonal elements are 0.
