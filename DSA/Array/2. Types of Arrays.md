# Types of Arrays

## Definition

Arrays can be classified based on **their size** and **their dimensions**. Understanding the different types of arrays helps in selecting the appropriate data structure for a particular problem.

The main types of arrays are:

* Static Arrays
* Dynamic Arrays
* One-Dimensional Arrays
* Two-Dimensional Arrays
* Multi-Dimensional Arrays
* Jagged Arrays

---

# 1. Static Arrays

A **Static Array** has a fixed size that is determined when the array is created. Once memory is allocated, its size cannot be changed.

If the array becomes full, a new larger array must be created, and the elements must be copied.

### Example

```java
int[] arr = new int[5];
```

### Advantages

* Simple implementation.
* Fast access to elements.
* Low memory overhead.

### Disadvantages

* Fixed size.
* Cannot grow or shrink dynamically.
* May lead to memory wastage or insufficient space.

---

# 2. Dynamic Arrays

A **Dynamic Array** automatically resizes itself when more space is required. When the current capacity is full, a larger array is created, and all existing elements are copied into the new array.

In Java, **ArrayList** is a dynamic array implementation.

### Example

```java
ArrayList<Integer> list = new ArrayList<>();
```

### Advantages

* Flexible size.
* Easy insertion at the end.
* Automatically manages memory.

### Disadvantages

* Resizing requires copying elements.
* Slightly higher memory overhead than static arrays.

---

# 3. One-Dimensional Array (1D Array)

A **One-Dimensional Array** stores elements in a single row. Each element is accessed using one index.

### Example

```java
int[] arr = {10, 20, 30, 40};
```

### Representation

```text
Index : 0   1   2   3
Value :10  20  30  40
```

---

# 4. Two-Dimensional Array (2D Array)

A **Two-Dimensional Array** stores data in rows and columns, forming a matrix.

Each element is accessed using two indices:

* Row index
* Column index

### Example

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6}
};
```

### Representation

```text
1  2  3
4  5  6
```

---

# 5. Multi-Dimensional Array

A **Multi-Dimensional Array** is an array containing more than two dimensions. It is mainly used for representing complex data such as 3D graphics, scientific computations, and simulations.

### Example

```java
int[][][] cube = new int[2][3][4];
```

---

# 6. Jagged Array

A **Jagged Array** is a two-dimensional array in which each row can have a different number of columns.

Unlike a regular 2D array, rows do not need to have the same length.

### Example

```java
int[][] jagged = {
    {1, 2},
    {3, 4, 5},
    {6}
};
```

### Representation

```text
1 2
3 4 5
6
```

Jagged arrays are supported in Java because a 2D array is actually an array of arrays.

---

# Comparison of Array Types

| Feature           | Static Array | Dynamic Array         |
| ----------------- | ------------ | --------------------- |
| Size              | Fixed        | Dynamic               |
| Memory Allocation | Once         | Resizes Automatically |
| Memory Overhead   | Low          | Slightly Higher       |
| Flexibility       | Low          | High                  |
| Example           | `int[]`      | `ArrayList`           |

---

| Array Type        | Dimensions         |
| ----------------- | ------------------ |
| One-Dimensional   | 1                  |
| Two-Dimensional   | 2                  |
| Multi-Dimensional | 3 or more          |
| Jagged Array      | Variable row sizes |

---

# Interview Questions

## 1. What is the difference between a static array and a dynamic array?

**Answer:**

| Static Array          | Dynamic Array          |
| --------------------- | ---------------------- |
| Fixed size            | Resizable              |
| Faster allocation     | Resizes automatically  |
| Lower memory overhead | Higher memory overhead |
| Example: `int[]`      | Example: `ArrayList`   |

---

## 2. What is a one-dimensional array?

**Answer:**
A one-dimensional array stores elements in a single row and uses one index to access each element.

---

## 3. What is a two-dimensional array?

**Answer:**
A two-dimensional array stores elements in rows and columns and is commonly used to represent matrices.

---

## 4. What is a multi-dimensional array?

**Answer:**
A multi-dimensional array has three or more dimensions and is used to represent complex data structures such as cubes, tensors, or multidimensional datasets.

---

## 5. What is a jagged array?

**Answer:**
A jagged array is an array of arrays where each row can have a different number of elements.

---

## 6. Why is `ArrayList` called a dynamic array?

**Answer:**
Because it automatically increases its capacity when the current storage becomes full, allowing elements to be added without manually resizing the array.

---

## 7. When should you use a static array?

**Answer:**
Use a static array when the number of elements is known in advance and will not change during program execution.

---

## 8. When should you use a dynamic array?

**Answer:**
Use a dynamic array when the number of elements is unknown or may change frequently during execution.

---

## 9. Can a 2D array have rows of different lengths?

**Answer:**
A regular 2D array typically has rows of equal length. However, in Java, a **jagged array** allows each row to have a different number of elements.

---

## 10. What is the main advantage of dynamic arrays over static arrays?

**Answer:**
The main advantage is flexibility. Dynamic arrays automatically resize as needed, eliminating the need to know the required size beforehand.
