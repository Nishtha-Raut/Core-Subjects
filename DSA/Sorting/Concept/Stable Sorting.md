# Stable Sorting

## Definition

A **Stable Sorting Algorithm** is a sorting algorithm that preserves the **relative order of equal elements** after sorting.

If two elements have the same value, their order in the input array remains the same in the sorted array.

Stability is an important property when sorting records based on multiple keys.

---

# Algorithm / Approach

### How Stability Works

Consider the following array:

```text
[(3, A), (1, B), (3, C), (2, D)]
```

Here,

* `3` appears twice.
* `A` comes before `C`.

After applying a **stable sorting algorithm**:

```text
[(1, B), (2, D), (3, A), (3, C)]
```

Notice that:

* Both `3`s are together.
* `A` still appears before `C`.

Their original order is preserved.

Now consider an unstable sorting algorithm:

```text
[(1, B), (2, D), (3, C), (3, A)]
```

Although the array is correctly sorted, the order of equal elements has changed.

Therefore, this sort is **not stable**.

---

# Complexity Analysis

Stability is **not a measure of complexity**.

It is a **property** of a sorting algorithm.

The time and space complexity depend on the specific sorting algorithm being used.

For example:

| Algorithm      | Time Complexity      | Stable                                |
| -------------- | -------------------- | ------------------------------------- |
| Bubble Sort    | O(n²)                | ✅ Yes                                 |
| Insertion Sort | O(n²)                | ✅ Yes                                 |
| Merge Sort     | O(n log n)           | ✅ Yes                                 |
| Counting Sort  | O(n + k)             | ✅ Yes                                 |
| Radix Sort     | O(d × (n + k))       | ✅ Yes                                 |
| Bucket Sort*   | O(n + k)             | ✅ Yes *(if bucket sorting is stable)* |
| Selection Sort | O(n²)                | ❌ No                                  |
| Quick Sort     | O(n log n) (Average) | ❌ No                                  |
| Heap Sort      | O(n log n)           | ❌ No                                  |

---

# Properties

| Property                 | Value                    |
| ------------------------ | ------------------------ |
| Preserves Relative Order | ✅ Yes                    |
| Comparison-Based         | Depends on the algorithm |
| Non-Comparison Based     | Depends on the algorithm |
| In-place                 | Depends on the algorithm |
| Adaptive                 | Depends on the algorithm |

---

# Interview Questions

## 1. What is a Stable Sorting Algorithm?

### Answer

A stable sorting algorithm preserves the relative order of elements having equal values after sorting.

---

## 2. What is meant by "relative order"?

### Answer

Relative order refers to the order in which equal elements appear in the original array.

For example:

```text
Input:
(5, A) (3, B) (5, C)

Stable Output:
(3, B) (5, A) (5, C)
```

`A` still appears before `C`.

---

## 3. Why is stability important?

### Answer

Stability is important when sorting data using **multiple keys**.

For example, suppose employee records are first sorted by **Name** and then by **Salary**. A stable sorting algorithm ensures that employees with the same salary remain ordered by their names.

---

## 4. Which sorting algorithms are stable?

### Answer

The commonly used stable sorting algorithms are:

* Bubble Sort
* Insertion Sort
* Merge Sort
* Counting Sort
* Radix Sort
* Bucket Sort (if the bucket sorting algorithm is stable)

---

## 5. Which sorting algorithms are unstable?

### Answer

The commonly used unstable sorting algorithms are:

* Selection Sort
* Quick Sort
* Heap Sort

---

## 6. Is Merge Sort stable?

### Answer

Yes.

During the merge process, if two elements are equal, Merge Sort places the element from the left subarray first, preserving the original order.

---

## 7. Is Quick Sort stable?

### Answer

No.

During partitioning, equal elements may be swapped, changing their original order.

---

## 8. Is Heap Sort stable?

### Answer

No.

Heap operations involve swapping elements, which can change the relative order of equal elements.

---

## 9. Is Selection Sort stable?

### Answer

No.

Selection Sort swaps the minimum element with the first unsorted element, which may change the order of equal elements.

---

## 10. Can an unstable sorting algorithm be made stable?

### Answer

Yes.

Some unstable algorithms can be modified to become stable by storing the original indices of elements or using additional memory. However, these modifications usually increase space complexity.

---

## 11. Does stability affect time complexity?

### Answer

No.

Stability is a property of a sorting algorithm and does not determine its time complexity.

---

## 12. Is Counting Sort stable?

### Answer

Yes.

Counting Sort becomes stable when the output array is built by traversing the input array from **right to left**.

---

## 13. Why must Radix Sort use a stable sorting algorithm?

### Answer

Each digit is sorted independently. A stable sorting algorithm preserves the order established by previous digit sorts, ensuring the final array is correctly sorted.

---

## 14. When should stable sorting be preferred?

### Answer

Stable sorting should be preferred when:

* Sorting records with multiple fields.
* The relative order of equal elements must be preserved.
* Applications such as databases, spreadsheets, and employee records require consistent ordering.

---

## 15. Is stability related to in-place sorting?

### Answer

No.

Stability and in-place sorting are independent properties. A sorting algorithm can be:

* Stable and in-place (Insertion Sort)
* Stable but not in-place (Merge Sort)
* In-place but not stable (Quick Sort)

---

## 16. How do you identify whether a sorting algorithm is stable?

### Answer

A sorting algorithm is stable if **equal elements appear in the same relative order after sorting as they did before sorting**. The easiest way to verify this is by labeling duplicate elements (e.g., `5A`, `5B`) and checking whether their order changes after sorting.
