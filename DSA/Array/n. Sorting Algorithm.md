# Sorting Algorithms

## Definition

**Sorting** is the process of arranging elements in a specific order, usually:

* Ascending order
* Descending order

Sorting is a fundamental concept in Data Structures and is widely used to improve efficiency of searching and processing data.

---

# Why Sorting is Important

Sorting helps in:

* Faster searching (Binary Search requires sorted array)
* Easier data analysis
* Efficient problem solving in algorithms
* Reducing complexity in many operations

---

# Types of Sorting Algorithms

Sorting algorithms are generally classified into:

* Comparison-based Sorting
* Non-comparison-based Sorting

---

# 1. Comparison-Based Sorting

These algorithms compare elements to determine order.

Examples:

* Bubble Sort
* Selection Sort
* Insertion Sort
* Merge Sort
* Quick Sort
* Heap Sort

---

# 2. Non-Comparison-Based Sorting

These do not compare elements directly.

Examples:

* Counting Sort
* Radix Sort
* Bucket Sort

---

# Key Sorting Algorithms Overview

---

## Bubble Sort

* Repeatedly swaps adjacent elements
* Largest element moves to end in each pass

### Time Complexity:

* Best: O(n)
* Average: O(n²)
* Worst: O(n²)

---

## Selection Sort

* Selects minimum element and places it at correct position

### Time Complexity:

* O(n²) in all cases

---

## Insertion Sort

* Builds sorted array one element at a time

### Time Complexity:

* Best: O(n)
* Worst: O(n²)

---

## Merge Sort

* Divide and conquer algorithm
* Splits array into halves and merges sorted halves

### Time Complexity:

* O(n log n)

---

## Quick Sort

* Uses pivot to partition array
* Recursively sorts partitions

### Time Complexity:

* Best: O(n log n)
* Worst: O(n²)

---

## Heap Sort

* Uses binary heap data structure
* Extracts maximum repeatedly

### Time Complexity:

* O(n log n)

---

## Counting Sort

* Uses frequency counting
* Works for small range of integers

### Time Complexity:

* O(n + k)

---

## Radix Sort

* Sorts digit by digit using stable sort

### Time Complexity:

* O(nk)

---

## Bucket Sort

* Distributes elements into buckets
* Sorts each bucket individually

### Time Complexity:

* O(n + k) (average case)

---

# Stability in Sorting

A sorting algorithm is **stable** if it maintains the relative order of equal elements.

Example:

```text id="a1"
(2a, 2b, 1)
After stable sort:
(1, 2a, 2b)
```

---

# In-Place Sorting

A sorting algorithm is **in-place** if it uses only a small constant amount of extra memory.

Examples:

* Bubble Sort
* Selection Sort
* Insertion Sort
* Quick Sort (mostly in-place)

---

# Divide and Conquer in Sorting

Used in:

* Merge Sort
* Quick Sort

### Steps:

* Divide array into smaller parts
* Conquer by sorting parts
* Combine results

---

# Complexity Summary

| Algorithm      | Best       | Average    | Worst      |
| -------------- | ---------- | ---------- | ---------- |
| Bubble Sort    | O(n)       | O(n²)      | O(n²)      |
| Selection Sort | O(n²)      | O(n²)      | O(n²)      |
| Insertion Sort | O(n)       | O(n²)      | O(n²)      |
| Merge Sort     | O(n log n) | O(n log n) | O(n log n) |
| Quick Sort     | O(n log n) | O(n log n) | O(n²)      |
| Heap Sort      | O(n log n) | O(n log n) | O(n log n) |
| Counting Sort  | O(n + k)   | O(n + k)   | O(n + k)   |
| Radix Sort     | O(nk)      | O(nk)      | O(nk)      |
| Bucket Sort    | O(n + k)   | O(n + k)   | O(n²)      |

---

# Applications of Sorting

* Searching optimization
* Database indexing
* Data analysis
* Scheduling problems
* Competitive programming
* System design tasks

---

# Advantages of Sorting

* Makes searching efficient
* Organizes data logically
* Simplifies problem solving
* Enables advanced algorithms like binary search

---

# Limitations

* Some algorithms are slow (O(n²))
* Extra space required in some cases (Merge Sort)
* Complex implementations for advanced sorts

---

# Interview Questions

## 1. What is sorting?

**Answer:**
Sorting is the process of arranging elements in a specific order, usually ascending or descending.

---

## 2. Why is sorting important?

**Answer:**
Sorting improves efficiency of searching and helps in organizing data for better processing.

---

## 3. What is the difference between comparison and non-comparison sorting?

**Answer:**

| Comparison           | Non-Comparison               |
| -------------------- | ---------------------------- |
| Uses comparisons     | Uses frequency/digit/buckets |
| Slower (generally)   | Faster for limited range     |
| Examples: Quick Sort | Counting Sort                |

---

## 4. What is stable sorting?

**Answer:**
A sorting algorithm is stable if it maintains the relative order of equal elements.

---

## 5. What is in-place sorting?

**Answer:**
In-place sorting uses only a constant amount of extra memory.

---

## 6. Which sorting algorithms use divide and conquer?

**Answer:**
Merge Sort and Quick Sort use divide and conquer strategy.

---

## 7. Which sorting algorithm is fastest?

**Answer:**
Merge Sort and Quick Sort are generally the fastest for large datasets (O(n log n)).

---

## 8. Why is Bubble Sort inefficient?

**Answer:**
Because it has O(n²) time complexity in average and worst cases.

---

## 9. When is Counting Sort preferred?

**Answer:**
When the range of numbers is small compared to the number of elements.

---

## 10. Why do we study multiple sorting algorithms?

**Answer:**
Because each algorithm has different use cases depending on data size, structure, and constraints.
