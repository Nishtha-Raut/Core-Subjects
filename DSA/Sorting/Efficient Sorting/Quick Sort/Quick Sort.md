# Quick Sort

## Definition

Quick Sort is a **divide-and-conquer** sorting algorithm that selects a **pivot** element and partitions the array such that elements smaller than the pivot are placed on its left, and elements greater than the pivot are placed on its right. The same process is then recursively applied to the left and right subarrays until the entire array is sorted.

Quick Sort is one of the fastest sorting algorithms in practice due to its excellent average-case performance and in-place nature. However, its worst-case time complexity is **O(n²)** when poor pivot choices are made.

---

# Algorithm / Approach

### Steps

1. Choose a pivot element from the array.
2. Rearrange (partition) the array so that:

   * Elements smaller than the pivot are placed on the left.
   * Elements greater than the pivot are placed on the right.
3. The pivot is now in its correct sorted position.
4. Recursively apply Quick Sort to the left subarray.
5. Recursively apply Quick Sort to the right subarray.
6. Continue until each subarray contains zero or one element.

---

# Complexity Analysis

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | **O(n log n)**  |
| Average Case | **O(n log n)**  |
| Worst Case   | **O(n²)**       |

### Space Complexity

* **Average Case:** **O(log n)** (Recursion Stack)
* **Worst Case:** **O(n)** (Recursion Stack)

Quick Sort is considered an **in-place** sorting algorithm because it rearranges elements within the original array and requires only recursion stack space.

---

# Properties

| Property          | Value              |
| ----------------- | ------------------ |
| Sorting Technique | Divide and Conquer |
| Stable            | ❌ No               |
| In-place          | ✅ Yes              |
| Adaptive          | ❌ No               |
| Recursive         | ✅ Yes              |

---

# Interview Questions

## 1. What is Quick Sort?

### Answer

Quick Sort is a divide-and-conquer sorting algorithm that selects a pivot element, partitions the array around it, and recursively sorts the left and right subarrays.

---

## 2. Why is it called Quick Sort?

### Answer

It is called Quick Sort because it is generally one of the fastest comparison-based sorting algorithms in practice, especially for large datasets.

---

## 3. How does Quick Sort work?

### Answer

Quick Sort selects a pivot element and partitions the array so that smaller elements come before the pivot and larger elements come after it. The pivot reaches its correct position, and the same process is recursively repeated for the left and right subarrays.

---

## 4. What is a pivot?

### Answer

A **pivot** is the element chosen to partition the array. Its final position after partitioning is the same as it would be in the fully sorted array.

---

## 5. What is partitioning?

### Answer

Partitioning is the process of rearranging the array so that:

* All elements smaller than the pivot are on its left.
* All elements larger than the pivot are on its right.

After partitioning, the pivot is in its correct sorted position.

---

## 6. What is the time complexity of Quick Sort?

### Answer

* **Best Case:** `O(n log n)`
* **Average Case:** `O(n log n)`
* **Worst Case:** `O(n²)`

---

## 7. Why is the average-case complexity `O(n log n)`?

### Answer

When the pivot divides the array into nearly equal halves, there are approximately **log₂(n)** levels of recursion, and each level processes all **n** elements during partitioning.

Therefore:

**Total Time = O(n) × O(log n) = O(n log n)**

---

## 8. Why is the worst-case complexity `O(n²)`?

### Answer

The worst case occurs when the pivot is always the smallest or largest element, creating highly unbalanced partitions (sizes `0` and `n - 1`). This results in `n` recursive levels with `O(n)` work at each level.

---

## 9. Is Quick Sort stable?

### Answer

No.

Quick Sort is **not stable** because partitioning may change the relative order of equal elements.

---

## 10. Is Quick Sort an in-place sorting algorithm?

### Answer

Yes.

Quick Sort rearranges elements within the original array and requires only recursion stack space, making it an in-place sorting algorithm.

---

## 11. Is Quick Sort adaptive?

### Answer

No.

Quick Sort performs partitioning regardless of whether the array is already sorted or nearly sorted.

---

## 12. Why is Quick Sort preferred over Merge Sort in arrays?

### Answer

Quick Sort is usually faster for arrays because:

* It sorts in-place.
* It has better cache performance.
* It requires less extra memory.

These factors often make it faster than Merge Sort in practical applications despite having the same average-case complexity.

---

## 13. What is the difference between Quick Sort and Merge Sort?

### Answer

| Quick Sort               | Merge Sort              |
| ------------------------ | ----------------------- |
| In-place                 | Not In-place            |
| Not Stable               | Stable                  |
| Average: `O(n log n)`    | Guaranteed `O(n log n)` |
| Worst: `O(n²)`           | Worst: `O(n log n)`     |
| Better cache performance | Requires extra memory   |

---

## 14. How can the worst case of Quick Sort be avoided?

### Answer

The worst case can be reduced by choosing a good pivot, such as:

* Random Pivot
* Median of Three
* Middle Element

These strategies make balanced partitions more likely and improve average performance.

---

## 15. Where is Quick Sort used?

### Answer

Quick Sort is commonly used for:

* Large arrays
* In-memory sorting
* General-purpose sorting
* Standard library implementations (with optimizations)

It is not suitable when a stable sort is required.

---

## 16. Why is Quick Sort considered one of the fastest sorting algorithms?

### Answer

Although its worst-case complexity is `O(n²)`, Quick Sort has low constant factors, performs in-place sorting, and has excellent cache locality. These characteristics make it one of the fastest sorting algorithms for most real-world applications.
