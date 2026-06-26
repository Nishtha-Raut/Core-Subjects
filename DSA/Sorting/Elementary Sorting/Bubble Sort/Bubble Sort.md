# Bubble Sort

## Definition

Bubble Sort is a simple comparison-based sorting algorithm that repeatedly compares two adjacent elements and swaps them if they are in the wrong order. After each pass, the largest unsorted element moves (or **bubbles**) to its correct position at the end of the array. This process continues until the entire array becomes sorted.

Bubble Sort is mainly used for learning sorting concepts because of its simple implementation. However, it is inefficient for large datasets due to its quadratic time complexity.

---

# Algorithm / Approach

### Steps

1. Start from the first element of the array.
2. Compare the current element with the next adjacent element.
3. If the current element is greater than the next element, swap them.
4. Continue comparing adjacent elements until the end of the array.
5. After the first pass, the largest element reaches its correct position.
6. Repeat the same process for the remaining unsorted portion of the array.
7. Continue until all elements are sorted.
8. In the optimized version, stop early if no swaps occur during a pass.

---

# Complexity Analysis

| Case                        | Time Complexity                    |
| --------------------------- | ---------------------------------- |
| Best Case (Already Sorted)  | **O(n)** *(Optimized Bubble Sort)* |
| Average Case                | **O(n²)**                          |
| Worst Case (Reverse Sorted) | **O(n²)**                          |

### Space Complexity

**O(1)**

Bubble Sort is an **in-place** sorting algorithm because it uses only a constant amount of extra memory.

---

# Properties

| Property          | Value                     |
| ----------------- | ------------------------- |
| Sorting Technique | Comparison-Based          |
| Stable            | ✅ Yes                     |
| In-place          | ✅ Yes                     |
| Adaptive          | ✅ Yes (Optimized Version) |
| Recursive         | ❌ No                      |

---

# Interview Questions

## 1. What is Bubble Sort?

### Answer

Bubble Sort is a comparison-based sorting algorithm that repeatedly compares adjacent elements and swaps them if they are in the wrong order. After each pass, the largest unsorted element reaches its correct position at the end of the array.

---

## 2. Why is it called Bubble Sort?

### Answer

It is called Bubble Sort because after every pass, the largest unsorted element gradually moves toward the end of the array, just like an air bubble rises to the surface of water.

---

## 3. How does Bubble Sort work?

### Answer

Bubble Sort repeatedly compares adjacent elements. Whenever two adjacent elements are in the wrong order, they are swapped. After each pass, the largest remaining unsorted element reaches its correct position. The process continues until the entire array is sorted.

---

## 4. Why are `n - 1` passes sufficient?

### Answer

After every pass, one element is placed in its final sorted position. Since there are `n` elements, after `n - 1` passes, the remaining element is automatically in its correct position.

---

## 5. What is the time complexity of Bubble Sort?

### Answer

* **Best Case:** `O(n)` (Optimized version)
* **Average Case:** `O(n²)`
* **Worst Case:** `O(n²)`

---

## 6. Why is the best-case complexity `O(n)`?

### Answer

In the optimized Bubble Sort, a boolean flag is used to check whether any swaps occur during a pass. If no swaps occur, it means the array is already sorted, so the algorithm terminates after one traversal, resulting in `O(n)` time complexity.

---

## 7. Why is the worst-case complexity `O(n²)`?

### Answer

In the worst case (reverse sorted array), every element must be compared and swapped in almost every pass. Therefore, the total number of comparisons becomes:

```
(n-1) + (n-2) + ... + 2 + 1
```

which is approximately `n²/2`, giving a time complexity of **O(n²)**.

---

## 8. Is Bubble Sort stable?

### Answer

Yes.

Bubble Sort is a **stable sorting algorithm** because equal elements are never swapped with each other. Their original relative order remains unchanged after sorting.

---

## 9. Is Bubble Sort an in-place sorting algorithm?

### Answer

Yes.

Bubble Sort uses only one temporary variable during swapping and does not require any additional data structure. Therefore, its space complexity is **O(1)**, making it an in-place sorting algorithm.

---

## 10. Is Bubble Sort adaptive?

### Answer

* **Basic Bubble Sort:** No
* **Optimized Bubble Sort:** Yes

The optimized version uses a swap flag. If no swaps occur during a pass, the algorithm stops immediately, making it adaptive for already sorted or nearly sorted arrays.

---

## 11. Why is Bubble Sort inefficient for large datasets?

### Answer

Bubble Sort performs a large number of comparisons and swaps, resulting in **O(n²)** time complexity. As the size of the array increases, the execution time grows rapidly, making it much slower than algorithms like Merge Sort, Quick Sort, and Heap Sort.

---

## 12. Why is Bubble Sort mainly used for teaching?

### Answer

Bubble Sort has a very simple logic and is easy to understand and implement. It helps beginners learn:

* Comparison-based sorting
* Swapping elements
* Nested loops
* Time complexity analysis

Although it is not efficient in practice, it serves as an excellent introductory sorting algorithm.

---

## 13. What is the difference between Bubble Sort and Selection Sort?

### Answer

| Bubble Sort                   | Selection Sort                                           |
| ----------------------------- | -------------------------------------------------------- |
| Swaps adjacent elements       | Finds the minimum element and performs one swap per pass |
| Stable                        | Not Stable                                               |
| Adaptive (Optimized)          | Not Adaptive                                             |
| More swaps                    | Fewer swaps                                              |
| Best Case: `O(n)` (Optimized) | Best Case: `O(n²)`                                       |

---

## 14. What is the difference between Bubble Sort and Insertion Sort?

### Answer

| Bubble Sort                        | Insertion Sort                                 |
| ---------------------------------- | ---------------------------------------------- |
| Swaps adjacent elements repeatedly | Inserts each element into its correct position |
| More swaps                         | Fewer swaps                                    |
| Slower for nearly sorted arrays    | Faster for nearly sorted arrays                |
| Easy to understand                 | More efficient in practice                     |

---

## 15. When should Bubble Sort be used?

### Answer

Bubble Sort is suitable for:

* Learning sorting algorithms
* Small datasets
* Already sorted or nearly sorted arrays (optimized version)
* Academic purposes

It is generally **not recommended** for large datasets or production applications due to its poor performance.

---

## 16. How can Bubble Sort be optimized?

### Answer

Bubble Sort can be optimized by using a **swap flag**.

* Initialize a boolean variable `swapped = false` at the beginning of each pass.
* Set it to `true` whenever a swap occurs.
* If no swaps occur during a complete pass, stop the algorithm because the array is already sorted.

This optimization reduces the best-case time complexity from **O(n²)** to **O(n)**.
