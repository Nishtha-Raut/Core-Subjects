# Radix Sort

## Definition

Radix Sort is a **non-comparison-based** sorting algorithm that sorts numbers digit by digit, starting from the **least significant digit (LSD)** or the **most significant digit (MSD)**. The most commonly used version is **LSD Radix Sort**, which processes digits from right to left.

Radix Sort uses a **stable sorting algorithm** (typically Counting Sort) to sort the numbers at each digit position. It is very efficient for sorting integers with a fixed number of digits.

---

# Algorithm / Approach

### Steps

1. Find the maximum element in the array.
2. Start with the least significant digit (units place).
3. Use a stable sorting algorithm (usually Counting Sort) to sort the array based on the current digit.
4. Move to the next digit (tens, hundreds, thousands, etc.).
5. Repeat the process until all digit positions have been processed.
6. After processing the most significant digit, the array becomes completely sorted.

---

# Complexity Analysis

| Case         | Time Complexity    |
| ------------ | ------------------ |
| Best Case    | **O(d × (n + k))** |
| Average Case | **O(d × (n + k))** |
| Worst Case   | **O(d × (n + k))** |

Where:

* **n** = Number of elements
* **d** = Number of digits in the largest element
* **k** = Range of each digit (10 for decimal numbers)

### Space Complexity

**O(n + k)**

* **O(n)** for the output array.
* **O(k)** for the count array used in Counting Sort.

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

## 1. What is Radix Sort?

### Answer

Radix Sort is a non-comparison-based sorting algorithm that sorts numbers one digit at a time using a stable sorting algorithm such as Counting Sort.

---

## 2. Why is it called Radix Sort?

### Answer

It is called Radix Sort because it sorts numbers based on their **radix (base)** and processes one digit position at a time.

---

## 3. How does Radix Sort work?

### Answer

Radix Sort repeatedly sorts the array according to each digit, starting from the least significant digit. After processing every digit position, the entire array becomes sorted.

---

## 4. Why does Radix Sort use Counting Sort?

### Answer

Counting Sort is:

* Stable
* Linear in time for a fixed digit range (0–9)

These properties make it ideal for sorting elements at each digit while preserving the order established in previous passes.

---

## 5. What is the time complexity of Radix Sort?

### Answer

* **Best Case:** `O(d × (n + k))`
* **Average Case:** `O(d × (n + k))`
* **Worst Case:** `O(d × (n + k))`

where:

* `d` = Number of digits
* `n` = Number of elements
* `k` = Range of each digit (10 for decimal numbers)

---

## 6. Why is Radix Sort considered linear for fixed-size integers?

### Answer

For fixed-size integers (such as 32-bit integers), the number of digits (`d`) is constant. Therefore, the complexity simplifies to **O(n)**.

---

## 7. Is Radix Sort stable?

### Answer

Yes.

Radix Sort is stable because it uses a stable sorting algorithm (Counting Sort) at each digit position.

---

## 8. Is Radix Sort an in-place sorting algorithm?

### Answer

No.

Radix Sort requires additional arrays during Counting Sort, resulting in a space complexity of **O(n + k)**.

---

## 9. Can Radix Sort handle negative numbers?

### Answer

The basic implementation cannot.

To support negative numbers, the array is typically divided into negative and non-negative values, sorted separately, and then combined.

---

## 10. Why is Radix Sort faster than comparison-based sorting for integers?

### Answer

Radix Sort avoids element comparisons and instead sorts based on digits. For fixed-size integers, this allows it to achieve near-linear time complexity.

---

## 11. What is the main disadvantage of Radix Sort?

### Answer

Radix Sort requires additional memory and works efficiently only for data types with a limited number of digits, such as integers or fixed-length strings.

---

## 12. What is the difference between Radix Sort and Counting Sort?

### Answer

| Radix Sort                    | Counting Sort                   |
| ----------------------------- | ------------------------------- |
| Sorts one digit at a time     | Sorts entire values             |
| Uses Counting Sort internally | Independent algorithm           |
| Works for larger ranges       | Efficient only for small ranges |
| Time: `O(d × (n + k))`        | Time: `O(n + k)`                |

---

## 13. Where is Radix Sort used?

### Answer

Radix Sort is commonly used for:

* Sorting integers
* Sorting fixed-length strings
* Phone numbers
* ZIP codes
* Student IDs
* Large datasets with bounded digit lengths

---

## 14. What is the difference between LSD and MSD Radix Sort?

### Answer

| LSD Radix Sort                          | MSD Radix Sort                         |
| --------------------------------------- | -------------------------------------- |
| Starts from the least significant digit | Starts from the most significant digit |
| Most commonly used                      | Less common                            |
| Simpler implementation                  | More complex implementation            |
| Works well for integers                 | Often used for strings                 |

---

## 15. Why must the sorting algorithm used in Radix Sort be stable?

### Answer

Each digit sort must preserve the order established by previous digit sorts. If the sorting algorithm is not stable, the final result will not be correctly sorted.

---

## 16. When should Radix Sort be used?

### Answer

Radix Sort is suitable when:

* The data consists of integers or fixed-length strings.
* The number of digits is limited.
* High performance is required for large datasets.

It is generally **not recommended** for floating-point numbers or data with highly variable lengths.
