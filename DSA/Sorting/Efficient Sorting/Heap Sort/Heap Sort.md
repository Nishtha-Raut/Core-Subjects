# Heap Sort

## Definition

Heap Sort is a **comparison-based** sorting algorithm that uses a **Binary Heap** data structure to sort elements. It first converts the array into a **Max Heap**, where the largest element is at the root. The root is then swapped with the last element, reducing the heap size by one. This process is repeated until the entire array is sorted.

Heap Sort guarantees **O(n log n)** time complexity in all cases and performs sorting in-place. However, it is **not stable** and is generally slower than Quick Sort in practice due to poor cache locality.

---

# Algorithm / Approach

### Steps

1. Build a **Max Heap** from the given array.
2. Swap the root element (maximum element) with the last element of the heap.
3. Reduce the heap size by one.
4. Heapify the root to restore the Max Heap property.
5. Repeat steps 2–4 until only one element remains.
6. The array is now sorted in ascending order.

---

# Complexity Analysis

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | **O(n log n)**  |
| Average Case | **O(n log n)**  |
| Worst Case   | **O(n log n)**  |

### Space Complexity

**O(1)**

Heap Sort is an **in-place** sorting algorithm because it requires only a constant amount of extra memory.

---

# Properties

| Property          | Value                                        |
| ----------------- | -------------------------------------------- |
| Sorting Technique | Comparison-Based (Heap-Based)                |
| Stable            | ❌ No                                         |
| In-place          | ✅ Yes                                        |
| Adaptive          | ❌ No                                         |
| Recursive         | ❌ No *(Can also be implemented recursively)* |

---

# Interview Questions

## 1. What is Heap Sort?

### Answer

Heap Sort is a comparison-based sorting algorithm that uses a Binary Heap to repeatedly extract the largest element and place it at the end of the array until the array becomes sorted.

---

## 2. Why is it called Heap Sort?

### Answer

It is called Heap Sort because it uses the **Heap** data structure, specifically a **Max Heap**, to perform sorting.

---

## 3. How does Heap Sort work?

### Answer

Heap Sort first converts the array into a Max Heap. The maximum element at the root is swapped with the last element, the heap size is reduced, and the heap is restored using the heapify operation. This process continues until all elements are sorted.

---

## 4. What is a Max Heap?

### Answer

A Max Heap is a complete binary tree in which every parent node is greater than or equal to its child nodes. Therefore, the root always contains the largest element.

---

## 5. What is Heapify?

### Answer

Heapify is the process of restoring the heap property after inserting, deleting, or swapping elements. It ensures that the subtree rooted at a given node satisfies the Max Heap property.

---

## 6. What is the time complexity of Heap Sort?

### Answer

* **Best Case:** `O(n log n)`
* **Average Case:** `O(n log n)`
* **Worst Case:** `O(n log n)`

Heap Sort performs consistently regardless of the input order.

---

## 7. Why is the time complexity `O(n log n)`?

### Answer

Building the Max Heap takes **O(n)** time. Each of the `n - 1` heap extractions requires a heapify operation, which takes **O(log n)** time.

Therefore,

**Total Time = O(n) + O(n log n) = O(n log n)**

---

## 8. Is Heap Sort stable?

### Answer

No.

Heap Sort is **not stable** because swapping elements during heap construction and extraction can change the relative order of equal elements.

---

## 9. Is Heap Sort an in-place sorting algorithm?

### Answer

Yes.

Heap Sort sorts the array within the original array and requires only constant extra memory, giving it a space complexity of **O(1)**.

---

## 10. Is Heap Sort adaptive?

### Answer

No.

Heap Sort performs the same operations regardless of whether the array is already sorted or completely unsorted.

---

## 11. Why is Heap Sort preferred over Merge Sort in some cases?

### Answer

Heap Sort requires only **O(1)** extra space, whereas Merge Sort requires **O(n)** extra space. Therefore, Heap Sort is useful when memory usage is an important consideration.

---

## 12. What is the difference between Heap Sort and Quick Sort?

### Answer

| Heap Sort                | Quick Sort           |
| ------------------------ | -------------------- |
| Guaranteed `O(n log n)`  | Average `O(n log n)` |
| Worst Case: `O(n log n)` | Worst Case: `O(n²)`  |
| In-place                 | In-place             |
| Not Stable               | Not Stable           |
| Slower in practice       | Faster in practice   |

---

## 13. What is the difference between Heap Sort and Merge Sort?

### Answer

| Heap Sort               | Merge Sort              |
| ----------------------- | ----------------------- |
| In-place                | Not In-place            |
| Not Stable              | Stable                  |
| `O(1)` Space            | `O(n)` Space            |
| Guaranteed `O(n log n)` | Guaranteed `O(n log n)` |

---

## 14. Where is Heap Sort used?

### Answer

Heap Sort is commonly used in:

* Systems with limited memory
* Priority Queue implementations
* Real-time systems
* Applications requiring guaranteed `O(n log n)` performance

---

## 15. Why is Heap Sort slower than Quick Sort in practice?

### Answer

Heap Sort has poor cache locality because elements that are compared are often far apart in memory. Quick Sort accesses nearby elements more frequently, making it faster on modern hardware.

---

## 16. Why is building a heap only `O(n)` and not `O(n log n)`?

### Answer

Although each individual heapify operation can take `O(log n)`, most heapify operations occur near the bottom of the tree, where the height is small. As a result, the total time to build the heap is **O(n)**, not **O(n log n)**.
