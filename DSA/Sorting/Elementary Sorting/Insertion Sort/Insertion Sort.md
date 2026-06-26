# Insertion Sort

## Definition

Insertion Sort is a comparison-based sorting algorithm that builds the sorted array one element at a time. It picks one element from the unsorted portion and inserts it into its correct position in the sorted portion by shifting larger elements one position to the right.

Insertion Sort is efficient for **small or nearly sorted datasets** because it minimizes unnecessary comparisons and shifts. Although its average and worst-case time complexities are **O(n²)**, its best-case complexity is **O(n)**.

---

# Algorithm / Approach

### Steps

1. Assume the first element is already sorted.
2. Pick the next element (called the **key**) from the unsorted portion.
3. Compare the key with elements in the sorted portion from right to left.
4. Shift all elements that are greater than the key one position to the right.
5. Insert the key into its correct position.
6. Repeat the process for all remaining elements until the array is sorted.

---

# Complexity Analysis

| Case                        | Time Complexity |
| --------------------------- | --------------- |
| Best Case (Already Sorted)  | **O(n)**        |
| Average Case                | **O(n²)**       |
| Worst Case (Reverse Sorted) | **O(n²)**       |

### Space Complexity

**O(1)**

Insertion Sort is an **in-place** sorting algorithm because it uses only a constant amount of extra memory.

---

# Properties

| Property          | Value            |
| ----------------- | ---------------- |
| Sorting Technique | Comparison-Based |
| Stable            | ✅ Yes            |
| In-place          | ✅ Yes            |
| Adaptive          | ✅ Yes            |
| Recursive         | ❌ No             |

---

# Interview Questions

## 1. What is Insertion Sort?

### Answer

Insertion Sort is a comparison-based sorting algorithm that builds a sorted array one element at a time by inserting each element into its correct position within the already sorted portion of the array.

---

## 2. Why is it called Insertion Sort?

### Answer

It is called Insertion Sort because each new element is **inserted** into its correct position in the sorted portion of the array.

---

## 3. How does Insertion Sort work?

### Answer

Insertion Sort starts with the first element as sorted. It then picks each remaining element one by one, compares it with the sorted portion, shifts larger elements to the right, and inserts the current element into its correct position.

---

## 4. Why does Insertion Sort start from the second element?

### Answer

The first element is considered already sorted because a single element is always sorted. Therefore, sorting begins from the second element.

---

## 5. What is the time complexity of Insertion Sort?

### Answer

* **Best Case:** `O(n)`
* **Average Case:** `O(n²)`
* **Worst Case:** `O(n²)`

---

## 6. Why is the best-case complexity `O(n)`?

### Answer

If the array is already sorted, every element only needs one comparison with its previous element, and no shifting is required. Therefore, the algorithm completes in linear time.

---

## 7. Why is the worst-case complexity `O(n²)`?

### Answer

In a reverse sorted array, every new element must be compared with all previously sorted elements and shifted to the beginning of the array. This results in approximately `n²/2` comparisons and shifts, giving a time complexity of `O(n²)`.

---

## 8. Is Insertion Sort stable?

### Answer

Yes.

Insertion Sort is a **stable sorting algorithm** because equal elements are not moved past one another, preserving their original relative order.

---

## 9. Is Insertion Sort an in-place sorting algorithm?

### Answer

Yes.

Insertion Sort sorts the array using only a few extra variables, so its space complexity is **O(1)**.

---

## 10. Is Insertion Sort adaptive?

### Answer

Yes.

Insertion Sort performs fewer operations when the array is already sorted or nearly sorted, making it an adaptive sorting algorithm.

---

## 11. Why is Insertion Sort efficient for nearly sorted arrays?

### Answer

In nearly sorted arrays, only a few elements are out of place. Therefore, very few comparisons and shifts are needed, allowing Insertion Sort to run close to **O(n)** time.

---

## 12. Why is Insertion Sort inefficient for large datasets?

### Answer

For large or randomly ordered datasets, many comparisons and shifts are required for each element, resulting in **O(n²)** time complexity. More efficient algorithms like Merge Sort and Quick Sort are preferred for such cases.

---

## 13. What is the difference between Insertion Sort and Bubble Sort?

### Answer

| Insertion Sort                                 | Bubble Sort                        |
| ---------------------------------------------- | ---------------------------------- |
| Inserts each element into its correct position | Swaps adjacent elements repeatedly |
| Performs shifts instead of many swaps          | Performs multiple swaps            |
| Faster for nearly sorted arrays                | Slower for nearly sorted arrays    |
| Adaptive                                       | Adaptive (Optimized Version)       |
| Stable                                         | Stable                             |

---

## 14. What is the difference between Insertion Sort and Selection Sort?

### Answer

| Insertion Sort                           | Selection Sort                             |
| ---------------------------------------- | ------------------------------------------ |
| Inserts elements into the sorted portion | Selects the minimum element in each pass   |
| Adaptive                                 | Not Adaptive                               |
| Stable                                   | Not Stable                                 |
| Better for nearly sorted arrays          | Same performance regardless of input order |
| Best Case: `O(n)`                        | Best Case: `O(n²)`                         |

---

## 15. When should Insertion Sort be used?

### Answer

Insertion Sort is suitable for:

* Small datasets
* Nearly sorted arrays
* Online sorting (data arriving one element at a time)
* Hybrid sorting algorithms (e.g., TimSort and IntroSort use Insertion Sort for small partitions)

It is generally **not recommended** for large randomly ordered datasets due to its `O(n²)` worst-case time complexity.

---

## 16. How many comparisons and shifts can Insertion Sort perform?

### Answer

For an array of size **n**:

* **Best Case Comparisons:** `n - 1`
* **Worst Case Comparisons:** `n(n - 1) / 2`
* **Worst Case Shifts:** `n(n - 1) / 2`

The number of shifts depends on how far each element must move to reach its correct position.
