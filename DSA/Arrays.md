# Arrays - Complete Interview Theory Notes

---

# Table of Contents

1. Introduction
2. Definition
3. Characteristics
4. Memory Representation
5. Address Calculation
6. Types of Arrays
7. Operations on Arrays
8. Time Complexities
9. Advantages
10. Disadvantages
11. Static vs Dynamic Arrays
12. Array Traversal
13. Searching Algorithms
14. Sorting Algorithms
15. Array Problem-Solving Techniques
16. Interview Questions
17. Important Formulas
18. Real-World Applications

---

# 1. Introduction

Array is one of the most fundamental data structures.

It is used to store multiple elements of the same data type in contiguous memory locations.

Arrays provide fast access to elements through indexing.

---

# 2. Definition

An array is a linear data structure that stores a fixed-size collection of elements of the same data type in contiguous memory locations.

Example:

int arr[] = {10,20,30,40,50};

Index:

0 1 2 3 4

Values:

10 20 30 40 50

---

# 3. Characteristics of Arrays

## 3.1 Fixed Size

Once created, size cannot be changed.

Example:

int arr[5];

Size remains 5.

---

## 3.2 Homogeneous Data

All elements must be of the same data type.

Valid:

int arr[] = {1,2,3};

Invalid:

{1,"hello",3}

---

## 3.3 Contiguous Memory Allocation

Elements are stored in adjacent memory locations.

Example:

Address   Value

1000      10
1004      20
1008      30
1012      40

(assuming integer size = 4 bytes)

---

## 3.4 Random Access

Any element can be accessed directly using index.

arr[3]

No traversal required.

Time Complexity:

O(1)

---

# 4. Memory Representation

Array elements occupy contiguous memory.

Example:

arr = [10,20,30,40]

Memory:

1000 -> 10
1004 -> 20
1008 -> 30
1012 -> 40

Each integer occupies 4 bytes.

---

# 5. Address Calculation Formula

Formula:

Address = Base Address + (Index × Size of Element)

Example:

Base Address = 1000

Find arr[3]

Address = 1000 + (3 × 4)

Address = 1012

---

# 6. Types of Arrays

## 6.1 One Dimensional Array

Stores elements in a single row.

Example:

int arr[] = {1,2,3,4};

---

## 6.2 Two Dimensional Array

Stores data in rows and columns.

Example:

int matrix[2][3];

1 2 3
4 5 6

Applications:

- Matrices
- Games
- Grids

---

## 6.3 Multi-Dimensional Array

Example:

int cube[3][3][3];

Used in:

- 3D graphics
- Scientific computations

---

## 6.4 Jagged Array (Java)

Rows can have different lengths.

Example:

int[][] arr = {
{1,2},
{3,4,5},
{6}
};

---

# 7. Array Operations

## 7.1 Access

Retrieve element by index.

arr[2]

Complexity:

O(1)

---

## 7.2 Update

Modify value at index.

arr[2] = 100;

Complexity:

O(1)

---

## 7.3 Traversal

Visit all elements.

for(int num : arr)

Complexity:

O(n)

---

## 7.4 Searching

Find element.

Complexity:

O(n)

---

## 7.5 Insertion

Insert element at position.

Requires shifting.

Complexity:

O(n)

---

## 7.6 Deletion

Remove element.

Requires shifting.

Complexity:

O(n)

---

# 8. Time Complexity Table

Operation              Complexity

Access                 O(1)

Update                 O(1)

Traversal              O(n)

Search                 O(n)

Insert Beginning       O(n)

Insert End             O(1)*

Delete Beginning       O(n)

Delete End             O(1)

*If space available

---

# 9. Advantages of Arrays

## Fast Access

O(1)

## Cache Friendly

Better CPU cache performance.

## Easy Traversal

Simple loops.

## Less Memory Overhead

No extra pointers.

---

# 10. Disadvantages of Arrays

## Fixed Size

Cannot grow automatically.

## Costly Insertion

Requires shifting.

## Costly Deletion

Requires shifting.

## Memory Wastage

Unused locations waste memory.

---

# 11. Static vs Dynamic Arrays

## Static Array

Size fixed.

Example:

int arr[10];

Advantages:

- Fast
- Simple

Disadvantages:

- Cannot resize

---

## Dynamic Array

Automatically grows.

Examples:

Java: ArrayList
C++: vector
Python: list

Advantages:

- Flexible

Disadvantages:

- Resizing cost

---

# 12. Searching Algorithms

---

## Linear Search

### Idea

Check each element one by one.

### Steps

1. Start from first element.
2. Compare.
3. Continue until found.

### Complexity

Best: O(1)

Average: O(n)

Worst: O(n)

### Use Cases

- Small arrays
- Unsorted arrays

---

## Binary Search

### Prerequisite

Array must be sorted.

### Idea

Divide search space into two halves.

### Steps

1. Find middle.
2. Compare target.
3. Eliminate one half.
4. Repeat.

### Complexity

Best: O(1)

Average: O(log n)

Worst: O(log n)

### Why Fast?

Each iteration removes half the elements.

---

# 13. Sorting Algorithms

---

## Bubble Sort

### Idea

Swap adjacent elements.

### Complexity

Best: O(n)

Worst: O(n²)

### Stable

Yes

### In-place

Yes

---

## Selection Sort

### Idea

Find minimum and place at correct position.

### Complexity

O(n²)

### Stable

No

### In-place

Yes

---

## Insertion Sort

### Idea

Insert current element into sorted portion.

### Complexity

Best: O(n)

Worst: O(n²)

### Stable

Yes

### In-place

Yes

---

## Merge Sort

### Technique

Divide and Conquer

### Steps

1. Divide array.
2. Sort halves.
3. Merge.

### Complexity

O(n log n)

### Stable

Yes

### In-place

No

---

## Quick Sort

### Technique

Divide and Conquer

### Pivot Selection

Choose pivot.

Partition array.

### Complexity

Best: O(n log n)

Average: O(n log n)

Worst: O(n²)

### Stable

No

### In-place

Yes

---

# 14. Important Array Techniques

---

## Prefix Sum

### Purpose

Fast range sum queries.

Array:

1 2 3 4 5

Prefix:

1 3 6 10 15

Complexity:

Build = O(n)

Query = O(1)

---

## Sliding Window

### Purpose

Subarray problems.

Instead of recalculating every window.

Move window efficiently.

Complexity:

O(n)

---

## Two Pointers

### Purpose

Pair problems.

Sorted arrays.

Complexity:

O(n)

---

## Kadane's Algorithm

### Purpose

Maximum Subarray Sum.

Complexity:

O(n)

---

## Difference Array

### Purpose

Efficient range updates.

Complexity:

O(1) update

O(n) final reconstruction

---

## Dutch National Flag Algorithm

### Purpose

Sort array containing:

0 1 2

Complexity:

O(n)

Space:

O(1)

---

# Arrays - Interview Questions and Answers

---

## Q1. What is an Array?

### Answer

An array is a linear data structure that stores elements of the same data type in contiguous memory locations.

### Key Characteristics

* Homogeneous data
* Fixed size
* Contiguous memory allocation
* Random access using index
* Memory efficient

### Example

```java
int[] arr = {10, 20, 30, 40};
```

---

## Q2. Why is Array Access O(1)?

### Answer

Array elements are stored in contiguous memory locations.

The address of any element can be calculated directly using:

```text
Address = Base Address + (Index × Size of Element)
```

Since no traversal is required, access takes constant time.

### Complexity

```text
Time Complexity: O(1)
```

---

## Q3. What is Contiguous Memory Allocation?

### Answer

Contiguous memory allocation means array elements are stored in consecutive memory locations.

### Example

```text
Address    Value

1000       10
1004       20
1008       30
1012       40
```

### Advantages

* Fast access
* Better cache utilization
* Efficient traversal

---

## Q4. Why is Insertion O(n)?

### Answer

When inserting an element at a specific position, existing elements must be shifted to create space.

### Example

Before:

```text
10 20 30 40
```

Insert 25 at index 2:

```text
10 20 25 30 40
```

Elements after index 2 must be shifted.

### Complexity

```text
Worst Case: O(n)
```

---

## Q5. Why is Deletion O(n)?

### Answer

After deletion, elements must be shifted left to fill the empty position.

### Example

Before:

```text
10 20 30 40
```

Delete 20:

```text
10 30 40
```

### Complexity

```text
Worst Case: O(n)
```

---

## Q6. Difference Between Array and Linked List

| Feature           | Array      | Linked List    |
| ----------------- | ---------- | -------------- |
| Memory Layout     | Contiguous | Non-Contiguous |
| Access            | O(1)       | O(n)           |
| Insertion         | O(n)       | O(1)*          |
| Deletion          | O(n)       | O(1)*          |
| Cache Performance | Better     | Worse          |
| Memory Usage      | Lower      | Higher         |

### Interview Answer

Arrays provide fast random access because elements are stored contiguously.

Linked Lists provide efficient insertion and deletion because nodes are connected using pointers.

---

## Q7. What are Dynamic Arrays?

### Answer

Dynamic arrays automatically increase their size when capacity is exceeded.

### Examples

* Java → ArrayList
* C++ → Vector
* Python → List

### Advantages

* Flexible size
* Easy insertion at end

### Disadvantages

* Resizing overhead
* Additional memory consumption

---

## Q8. What is Array Traversal?

### Answer

Traversal means visiting every element exactly once.

### Example

```java
for(int num : arr){
    System.out.println(num);
}
```

### Complexity

```text
O(n)
```

---

## Q9. Explain Linear Search

### Answer

Linear Search checks elements one by one until the target is found.

### Steps

1. Start from first element.
2. Compare with target.
3. Continue until found.

### Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(1)       |
| Average | O(n)       |
| Worst   | O(n)       |

### Use Cases

* Small arrays
* Unsorted arrays

---

## Q10. Explain Binary Search

### Answer

Binary Search finds an element in a sorted array by repeatedly dividing the search space into two halves.

### Prerequisite

Array must be sorted.

### Steps

1. Find middle element.
2. Compare with target.
3. Search left half or right half.
4. Repeat until found.

### Complexity

```text
O(log n)
```

### Why is Binary Search Fast?

Because every comparison eliminates half of the remaining elements.

---

## Q11. Explain Prefix Sum

### Answer

Prefix Sum stores cumulative sums of elements.

### Example

Array:

```text
1 2 3 4 5
```

Prefix Sum:

```text
1 3 6 10 15
```

### Formula

```text
prefix[i] = prefix[i-1] + arr[i]
```

### Complexity

```text
Build: O(n)
Query: O(1)
```

### Applications

* Range sum queries
* Competitive programming
* Analytics systems

---

## Q12. Explain Sliding Window

### Answer

Sliding Window is used to efficiently process contiguous subarrays.

Instead of recalculating each window, move the window by removing one element and adding another.

### Complexity

```text
O(n)
```

### Applications

* Maximum sum subarray of size K
* Longest substring problems
* Streaming data

---

## Q13. Explain Two Pointers Technique

### Answer

Two Pointers uses two indices moving through the array to solve problems efficiently.

### Example

```text
1 2 3 4 5 6
```

One pointer starts from left and another from right.

### Applications

* Pair Sum
* Remove Duplicates
* Container With Most Water
* Sorted Array Problems

### Complexity

```text
O(n)
```

---

## Q14. Explain Kadane's Algorithm

### Answer

Kadane's Algorithm finds the maximum sum contiguous subarray.

### Idea

At every index:

* Extend current subarray
* Or start a new subarray

### Complexity

```text
O(n)
```

### Example

```text
-2 1 -3 4 -1 2 1 -5 4
```

Maximum Sum:

```text
6
```

Subarray:

```text
4 -1 2 1
```

---

## Q15. Explain Dutch National Flag Algorithm

### Answer

Used for sorting arrays containing only:

```text
0 1 2
```

### Pointers Used

```text
low
mid
high
```

### Complexity

```text
Time: O(n)
Space: O(1)
```

### Applications

* Sort Colors problem
* Three-way partitioning

---

## Q16. Why is Merge Sort Stable?

### Answer

Merge Sort preserves the relative order of equal elements while merging.

### Example

Before:

```text
5A 3 5B
```

After:

```text
3 5A 5B
```

Relative order of equal elements remains unchanged.

---

## Q17. Why is Quick Sort Usually Faster Than Merge Sort?

### Answer

Quick Sort is generally faster because:

* Better cache locality
* No extra array required
* In-place sorting
* Lower constant factors

### Complexity

```text
Average: O(n log n)
Worst: O(n²)
```

---

## Q18. What is Cache Locality?

### Answer

Cache locality means data stored close together can be loaded into CPU cache efficiently.

Arrays have excellent cache locality because elements are stored contiguously.

### Benefits

* Faster execution
* Better CPU utilization

---

## Q19. When Would You Choose an Array?

### Answer

Use arrays when:

* Fast random access is required
* Data size is mostly fixed
* Frequent index-based operations are needed
* Memory efficiency is important

---

## Q20. What are the Limitations of Arrays?

### Answer

1. Fixed size
2. Costly insertion
3. Costly deletion
4. Homogeneous data only
5. Requires contiguous memory

---

# 30-Second Placement Answer

## Tell Me About Arrays

An array is a linear data structure that stores elements of the same data type in contiguous memory locations. It provides O(1) random access because the address of an element can be calculated directly using its index. Arrays are memory efficient and cache friendly, but insertion and deletion operations are expensive because elements need to be shifted. Arrays are widely used when fast access and efficient memory usage are required.

---

# 16. Important Formulas

Address Calculation:

Address = Base + (Index × Size)

Binary Search Complexity:

O(log n)

Traversal:

O(n)

Insertion:

O(n)

Deletion:

O(n)

Access:

O(1)

---

# 17. Real World Applications

## Databases

Record storage.

## Image Processing

Pixels stored as arrays.

## Matrices

Scientific computations.

## Games

Board representation.

## Operating Systems

Memory management.

## Machine Learning

Feature vectors.

---
