# Selection Sort

## Definition

Selection Sort is a simple comparison-based sorting algorithm that repeatedly finds the smallest element from the unsorted portion of the array and places it at its correct position in the sorted portion. With each pass, the sorted portion of the array grows by one element until the entire array is sorted.

Unlike Bubble Sort, Selection Sort performs **only one swap per pass**, making it useful when the cost of swapping is high. However, it still has a quadratic time complexity and is inefficient for large datasets.

---

# Algorithm / Approach

### Steps

1. Assume the first unsorted element is the minimum.
2. Traverse the remaining unsorted portion of the array to find the actual minimum element.
3. Swap the minimum element with the first unsorted element.
4. Move the boundary of the sorted portion one position to the right.
5. Repeat the same process for the remaining unsorted elements.
6. Continue until all elements are sorted.

---

# Complexity Analysis

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | **O(n²)**       |
| Average Case | **O(n²)**       |
| Worst Case   | **O(n²)**       |

### Space Complexity

**O(1)**

Selection Sort is an **in-place** sorting algorithm because it requires only a constant amount of extra memory.

---

# Properties

| Property          | Value            |
| ----------------- | ---------------- |
| Sorting Technique | Comparison-Based |
| Stable            | ❌ No             |
| In-place          | ✅ Yes            |
| Adaptive          | ❌ No             |
| Recursive         | ❌ No             |

---

# Interview Questions

## 1. What is Selection Sort?

### Answer

Selection Sort is a comparison-based sorting algorithm that repeatedly selects the smallest element from the unsorted portion of the array and places it at the beginning of that portion by swapping it with the first unsorted element.

---

## 2. Why is it called Selection Sort?

### Answer

It is called Selection Sort because in every pass, the algorithm **selects** the minimum element from the unsorted portion and places it in its correct position.

---

## 3. How does Selection Sort work?

### Answer

Selection Sort divides the array into two parts:

* Sorted portion
* Unsorted portion

During each pass, it finds the smallest element in the unsorted portion and swaps it with the first unsorted element. This process continues until the array is completely sorted.

---

## 4. Why are `n - 1` passes sufficient?

### Answer

After each pass, one element is placed in its final sorted position. Therefore, after `n - 1` passes, only one element remains, which is automatically in the correct position.

---

## 5. What is the time complexity of Selection Sort?

### Answer

* **Best Case:** `O(n²)`
* **Average Case:** `O(n²)`
* **Worst Case:** `O(n²)`

The algorithm always scans the remaining unsorted portion to find the minimum element, regardless of whether the array is already sorted.

---

## 6. Why is the best-case complexity also `O(n²)`?

### Answer

Even if the array is already sorted, Selection Sort still compares every element in the unsorted portion to find the minimum. Since the number of comparisons remains the same, the best-case time complexity is also `O(n²)`.

---

## 7. Why is Selection Sort not adaptive?

### Answer

Selection Sort always performs the same number of comparisons regardless of the initial order of the elements. It cannot detect whether the array is already sorted or nearly sorted.

---

## 8. Is Selection Sort stable?

### Answer

No.

Selection Sort is **not stable** because swapping the minimum element with the first unsorted element can change the relative order of equal elements.

---

## 9. Is Selection Sort an in-place sorting algorithm?

### Answer

Yes.

Selection Sort uses only one temporary variable for swapping and does not require any extra data structure. Therefore, its space complexity is **O(1)**.

---

## 10. Why is Selection Sort inefficient for large datasets?

### Answer

Selection Sort performs approximately `n(n - 1)/2` comparisons in every case, resulting in **O(n²)** time complexity. This makes it much slower than efficient algorithms like Merge Sort, Quick Sort, and Heap Sort for large datasets.

---

## 11. What is the main advantage of Selection Sort over Bubble Sort?

### Answer

Selection Sort performs **at most one swap per pass**, whereas Bubble Sort may perform multiple swaps in a single pass. Therefore, Selection Sort is preferred when the cost of swapping is high.

---

## 12. What is the main disadvantage of Selection Sort?

### Answer

Its biggest disadvantage is that it always performs **O(n²)** comparisons, even when the array is already sorted, making it inefficient for large or nearly sorted datasets.

---

## 13. What is the difference between Selection Sort and Bubble Sort?

### Answer

| Selection Sort                         | Bubble Sort                        |
| -------------------------------------- | ---------------------------------- |
| Finds the minimum element in each pass | Swaps adjacent elements repeatedly |
| One swap per pass                      | Multiple swaps per pass            |
| Not Stable                             | Stable                             |
| Not Adaptive                           | Adaptive (Optimized Version)       |
| Best Case: `O(n²)`                     | Best Case: `O(n)` (Optimized)      |

---

## 14. What is the difference between Selection Sort and Insertion Sort?

### Answer

| Selection Sort                      | Insertion Sort                                       |
| ----------------------------------- | ---------------------------------------------------- |
| Finds the minimum element           | Inserts each element into its correct position       |
| Always performs `O(n²)` comparisons | Faster for nearly sorted arrays                      |
| Not Adaptive                        | Adaptive                                             |
| Fewer swaps                         | More shifts, fewer comparisons on nearly sorted data |

---

## 15. When should Selection Sort be used?

### Answer

Selection Sort is suitable for:

* Learning sorting algorithms
* Small datasets
* Situations where minimizing the number of swaps is important

It is generally **not recommended** for large datasets due to its `O(n²)` time complexity.

---

## 16. How many comparisons and swaps does Selection Sort perform?

### Answer

For an array of size **n**:

* **Comparisons:** `n(n - 1) / 2`
* **Maximum Swaps:** `n - 1`

This is one of the key characteristics of Selection Sort—it performs very few swaps compared to Bubble Sort.
