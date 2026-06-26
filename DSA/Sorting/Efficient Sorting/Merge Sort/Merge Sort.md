# Merge Sort

## Definition

Merge Sort is a **divide-and-conquer** sorting algorithm that recursively divides the array into two halves until each subarray contains a single element. It then merges the sorted subarrays to produce a completely sorted array.

Merge Sort is one of the most efficient sorting algorithms with a guaranteed time complexity of **O(n log n)** in all cases. However, it requires additional memory for the merging process.

---

# Algorithm / Approach

### Steps

1. If the array contains only one element, it is already sorted.
2. Divide the array into two equal halves.
3. Recursively apply Merge Sort to the left half.
4. Recursively apply Merge Sort to the right half.
5. Merge the two sorted halves into a single sorted array.
6. Repeat the process until the entire array is sorted.

---

# Complexity Analysis

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | **O(n log n)**  |
| Average Case | **O(n log n)**  |
| Worst Case   | **O(n log n)**  |

### Space Complexity

**O(n)**

Merge Sort requires an additional temporary array during the merging process, making it **not an in-place** sorting algorithm.

---

# Properties

| Property          | Value              |
| ----------------- | ------------------ |
| Sorting Technique | Divide and Conquer |
| Stable            | ✅ Yes              |
| In-place          | ❌ No               |
| Adaptive          | ❌ No               |
| Recursive         | ✅ Yes              |

---

# Interview Questions

## 1. What is Merge Sort?

### Answer

Merge Sort is a divide-and-conquer sorting algorithm that recursively divides an array into smaller subarrays, sorts them, and then merges them to produce a sorted array.

---

## 2. Why is it called Merge Sort?

### Answer

It is called Merge Sort because the final sorted array is obtained by **merging** multiple sorted subarrays.

---

## 3. How does Merge Sort work?

### Answer

Merge Sort repeatedly divides the array into two halves until each subarray contains only one element. Since a single element is already sorted, it then merges these subarrays in sorted order until the complete array is sorted.

---

## 4. What is the Divide and Conquer technique?

### Answer

Divide and Conquer is an algorithm design technique that solves a problem by:

1. Dividing it into smaller subproblems.
2. Solving each subproblem recursively.
3. Combining the solutions to obtain the final result.

Merge Sort is a classic example of this technique.

---

## 5. What is the time complexity of Merge Sort?

### Answer

* **Best Case:** `O(n log n)`
* **Average Case:** `O(n log n)`
* **Worst Case:** `O(n log n)`

Its time complexity remains the same regardless of the input order.

---

## 6. Why is the time complexity `O(n log n)`?

### Answer

The array is divided into halves **log₂(n)** times, and at each level, merging all elements takes **O(n)** time.

Therefore,

**Total Time = O(n) × O(log n) = O(n log n)**

---

## 7. Why is Merge Sort stable?

### Answer

Merge Sort is stable because when two equal elements are encountered during merging, the element from the left subarray is placed first. This preserves the original relative order of equal elements.

---

## 8. Is Merge Sort an in-place sorting algorithm?

### Answer

No.

Merge Sort requires an additional temporary array during the merge step. Therefore, its space complexity is **O(n)**.

---

## 9. Is Merge Sort adaptive?

### Answer

No.

Merge Sort performs the same sequence of divisions and merges regardless of whether the array is already sorted or completely unsorted.

---

## 10. Why is Merge Sort preferred for large datasets?

### Answer

Merge Sort guarantees **O(n log n)** time complexity in all cases, making it much more efficient than quadratic algorithms such as Bubble Sort, Selection Sort, and Insertion Sort for large datasets.

---

## 11. What is the main disadvantage of Merge Sort?

### Answer

The main disadvantage is its **O(n)** extra space requirement due to the temporary array used during merging. This increases memory usage compared to in-place algorithms.

---

## 12. What is the difference between Merge Sort and Quick Sort?

### Answer

| Merge Sort              | Quick Sort                          |
| ----------------------- | ----------------------------------- |
| Divide and Conquer      | Divide and Conquer                  |
| Guaranteed `O(n log n)` | Average `O(n log n)`, Worst `O(n²)` |
| Stable                  | Not Stable                          |
| Not In-place            | In-place                            |
| Requires extra memory   | Requires very little extra memory   |

---

## 13. What is the difference between Merge Sort and Insertion Sort?

### Answer

| Merge Sort                  | Insertion Sort                               |
| --------------------------- | -------------------------------------------- |
| `O(n log n)`                | `O(n²)`                                      |
| Suitable for large datasets | Suitable for small or nearly sorted datasets |
| Uses recursion              | Uses iteration                               |
| Requires extra space        | In-place                                     |

---

## 14. Where is Merge Sort used?

### Answer

Merge Sort is commonly used in:

* External sorting (sorting files stored on disk)
* Linked List sorting
* Stable sorting applications
* Large datasets
* Parallel processing

---

## 15. Can Merge Sort be used for Linked Lists?

### Answer

Yes.

Merge Sort is one of the best sorting algorithms for linked lists because splitting and merging linked lists can be performed efficiently without requiring random access.

---

## 16. Why is Merge Sort considered a stable sorting algorithm?

### Answer

During the merge step, when two equal elements are compared, the element from the left subarray is chosen first. This ensures that equal elements maintain their original relative order after sorting.
