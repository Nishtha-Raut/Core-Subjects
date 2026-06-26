# Counting Sort

## Definition

Counting Sort is a **non-comparison-based** sorting algorithm that sorts elements by counting the frequency of each distinct value. Instead of comparing elements, it counts how many times each value occurs and then reconstructs the sorted array using these counts.

Counting Sort is very efficient when the range of input values is small. It is commonly used as a subroutine in algorithms like **Radix Sort**.

---

# Algorithm / Approach

### Steps

1. Find the maximum element in the array.
2. Create a count array of size `max + 1`.
3. Count the frequency of each element.
4. Compute the cumulative (prefix) sum of the count array.
5. Create an output array.
6. Traverse the original array from right to left and place each element in its correct position using the count array.
7. Copy the sorted output array back to the original array.

---

# Complexity Analysis

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | **O(n + k)**    |
| Average Case | **O(n + k)**    |
| Worst Case   | **O(n + k)**    |

Where:

* **n** = Number of elements
* **k** = Range of input values (Maximum Value + 1)

### Space Complexity

**O(n + k)**

* **O(n)** for the output array.
* **O(k)** for the count array.

---

# Properties

| Property          | Value                |
| ----------------- | -------------------- |
| Sorting Technique | Non-Comparison Based |
| Stable            | ✅ Yes                |
| In-place          | ❌ No                 |
| Adaptive          | ❌ No                 |
| Recursive         | ❌ No                 |

---

# Interview Questions

## 1. What is Counting Sort?

### Answer

Counting Sort is a non-comparison-based sorting algorithm that sorts elements by counting the frequency of each value instead of comparing elements.

---

## 2. Why is it called Counting Sort?

### Answer

It is called Counting Sort because it sorts elements by **counting** how many times each value appears in the input array.

---

## 3. How does Counting Sort work?

### Answer

Counting Sort counts the frequency of every element, converts these frequencies into cumulative counts, places each element into its correct position in an output array, and finally copies the sorted elements back to the original array.

---

## 4. Why is Counting Sort not a comparison-based algorithm?

### Answer

Counting Sort never compares two elements. Instead, it relies on the frequency of values to determine their sorted positions.

---

## 5. What is the time complexity of Counting Sort?

### Answer

* **Best Case:** `O(n + k)`
* **Average Case:** `O(n + k)`
* **Worst Case:** `O(n + k)`

where:

* `n` = Number of elements
* `k` = Range of input values

---

## 6. Why is the time complexity `O(n + k)`?

### Answer

The algorithm performs:

* One traversal to find the maximum element.
* One traversal to count frequencies.
* One traversal over the count array.
* One traversal to build the output array.

Hence, the total complexity becomes **O(n + k)**.

---

## 7. Is Counting Sort stable?

### Answer

Yes.

Counting Sort is stable when elements are inserted into the output array by traversing the original array from **right to left**. This preserves the relative order of equal elements.

---

## 8. Is Counting Sort an in-place sorting algorithm?

### Answer

No.

Counting Sort requires both a count array and an output array, resulting in a space complexity of **O(n + k)**.

---

## 9. Can Counting Sort handle negative numbers?

### Answer

The basic version cannot.

However, it can be modified by shifting all values using the minimum element so that every value maps to a non-negative index.

---

## 10. Why is Counting Sort efficient only for a small range of values?

### Answer

The size of the count array depends on the maximum value in the input. If the range (`k`) is very large compared to the number of elements (`n`), the algorithm wastes memory and becomes inefficient.

---

## 11. What is the main disadvantage of Counting Sort?

### Answer

Its main disadvantage is the large memory requirement when the range of values is large. It is not suitable for data with widely spread values.

---

## 12. What is the difference between Counting Sort and Comparison-Based Sorting?

### Answer

| Counting Sort                | Comparison-Based Sort           |
| ---------------------------- | ------------------------------- |
| No comparisons               | Compares elements               |
| Time: `O(n + k)`             | Usually `O(n log n)`            |
| Requires limited value range | Works for any comparable values |
| Uses extra memory            | Some algorithms are in-place    |

---

## 13. Where is Counting Sort used?

### Answer

Counting Sort is commonly used for:

* Sorting integers with a small range
* Radix Sort
* Bucket Sort
* Frequency-based problems
* Competitive programming

---

## 14. What type of data is suitable for Counting Sort?

### Answer

Counting Sort works best when:

* The data consists of integers.
* The range of values is small.
* The number of elements is large compared to the value range.

---

## 15. What is the difference between Counting Sort and Radix Sort?

### Answer

| Counting Sort          | Radix Sort                    |
| ---------------------- | ----------------------------- |
| Sorts entire numbers   | Sorts one digit at a time     |
| Used independently     | Uses Counting Sort internally |
| Depends on value range | Depends on number of digits   |

---

## 16. Why is Counting Sort used in Radix Sort?

### Answer

Radix Sort requires a **stable** sorting algorithm for each digit. Counting Sort is stable and runs in linear time for a limited range of digit values (0–9), making it an ideal choice.
