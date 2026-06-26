# In-Place Sorting

## Definition

An **In-Place Sorting Algorithm** is a sorting algorithm that sorts the elements within the original array using only a **constant amount of extra memory**. It rearranges the elements without creating another array proportional to the input size.

An algorithm is generally considered in-place if its extra space complexity is **O(1)**. (The recursion stack, if any, is often treated separately.)

---

# Algorithm / Approach

### How In-Place Sorting Works

Instead of creating a new array, an in-place sorting algorithm rearranges elements inside the original array using operations such as swapping or shifting.

Example:

```text
Input:
[5, 2, 8, 1]
```

After sorting:

```text
[1, 2, 5, 8]
```

The same array is modified to produce the sorted output without allocating another array of size `n`.

---

# Complexity Analysis

In-place sorting is a **property**, not a measure of complexity.

An algorithm is considered in-place if it requires **O(1)** extra space.

Examples:

| Algorithm      | Extra Space | In-Place |
| -------------- | ----------- | -------- |
| Bubble Sort    | O(1)        | ✅ Yes    |
| Selection Sort | O(1)        | ✅ Yes    |
| Insertion Sort | O(1)        | ✅ Yes    |
| Quick Sort     | O(log n)*   | ✅ Yes    |
| Heap Sort      | O(1)        | ✅ Yes    |
| Merge Sort     | O(n)        | ❌ No     |
| Counting Sort  | O(n + k)    | ❌ No     |
| Radix Sort     | O(n + k)    | ❌ No     |
| Bucket Sort    | O(n + k)    | ❌ No     |

> **Note:** Quick Sort is generally considered in-place because it rearranges elements within the original array. The `O(log n)` space is due to the recursion stack, not an auxiliary array.

---

# Properties

| Property                | Value                              |
| ----------------------- | ---------------------------------- |
| Uses Original Array     | ✅ Yes                              |
| Extra Space             | O(1) *(excluding recursion stack)* |
| Creates Auxiliary Array | ❌ No                               |
| Memory Efficient        | ✅ Yes                              |
| Stable                  | Depends on the algorithm           |

---

# Interview Questions

## 1. What is an In-Place Sorting Algorithm?

### Answer

An in-place sorting algorithm sorts elements within the original array using only a constant amount of extra memory, typically **O(1)**.

---

## 2. Why is it called In-Place Sorting?

### Answer

It is called in-place sorting because the sorting is performed **in the same memory location** where the original data is stored, without creating another array of comparable size.

---

## 3. Why is In-Place Sorting important?

### Answer

In-place sorting reduces memory usage, making it suitable for memory-constrained systems and large datasets where allocating additional memory is expensive.

---

## 4. Which sorting algorithms are in-place?

### Answer

The commonly used in-place sorting algorithms are:

* Bubble Sort
* Selection Sort
* Insertion Sort
* Quick Sort
* Heap Sort

---

## 5. Which sorting algorithms are not in-place?

### Answer

The commonly used non-in-place sorting algorithms are:

* Merge Sort
* Counting Sort
* Radix Sort
* Bucket Sort

---

## 6. Why is Merge Sort not in-place?

### Answer

Merge Sort requires a temporary array to merge two sorted halves. Therefore, its extra space complexity is **O(n)**.

---

## 7. Is Quick Sort in-place?

### Answer

Yes.

Quick Sort rearranges elements within the original array using partitioning. Although it uses recursion, the extra memory comes only from the recursion stack, so it is generally considered an in-place sorting algorithm.

---

## 8. Is Heap Sort in-place?

### Answer

Yes.

Heap Sort builds and maintains the heap within the original array and requires only a few extra variables for swapping.

---

## 9. Is Counting Sort in-place?

### Answer

No.

Counting Sort requires a count array and an output array, resulting in an extra space complexity of **O(n + k)**.

---

## 10. Is Bucket Sort in-place?

### Answer

No.

Bucket Sort creates multiple buckets to store elements before sorting them, requiring additional memory.

---

## 11. Does in-place sorting mean no extra memory is used?

### Answer

No.

In-place sorting allows a **constant amount of extra memory (O(1))**, such as temporary variables used for swapping. It simply means that no extra array proportional to the input size is required.

---

## 12. Does recursion affect whether an algorithm is in-place?

### Answer

Recursion uses stack memory. Algorithms like Quick Sort require **O(log n)** recursion stack space on average but are still generally classified as in-place because they do not allocate an auxiliary array.

---

## 13. What is the main advantage of in-place sorting?

### Answer

The main advantage is **low memory usage**, making it efficient for systems with limited memory and large datasets.

---

## 14. What is the main disadvantage of in-place sorting?

### Answer

Some in-place algorithms may be less stable or harder to implement compared to algorithms that use additional memory.

---

## 15. Can an algorithm be both stable and in-place?

### Answer

Yes.

For example:

* **Bubble Sort**
* **Insertion Sort**

Both are stable and in-place.

However, not all in-place algorithms are stable (e.g., Quick Sort and Heap Sort).

---

## 16. What is the difference between Stable Sorting and In-Place Sorting?

### Answer

| Stable Sorting                                 | In-Place Sorting                            |
| ---------------------------------------------- | ------------------------------------------- |
| Preserves the relative order of equal elements | Uses only a constant amount of extra memory |
| Focuses on element order                       | Focuses on memory usage                     |
| May or may not be in-place                     | May or may not be stable                    |
| Example: Merge Sort                            | Example: Heap Sort                          |
