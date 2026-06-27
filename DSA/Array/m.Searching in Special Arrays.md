# Searching in Special Arrays

## Definition

**Searching in Special Arrays** refers to applying modified search techniques (mostly based on Binary Search) on arrays that follow a **specific pattern or property** rather than being strictly sorted.

These problems require identifying the structure of the array and adapting the search logic accordingly.

---

# 1. Rotated Sorted Array

A sorted array that has been rotated at a pivot point.

Example:

```text id="a1"
[4, 5, 6, 7, 0, 1, 2]
```

## Idea

* One part of the array is always sorted.
* Identify the sorted half.
* Decide where the target lies.

## Applications

* Search in rotated array
* Find minimum in rotated array

---

# 2. Nearly Sorted Array

An array where elements may be misplaced by at most one position.

Example:

```text id="a2"
[2, 1, 4, 3, 5]
```

## Idea

Check:

* `mid`
* `mid - 1`
* `mid + 1`

Then move accordingly.

---

# 3. Bitonic Array

An array that first increases and then decreases.

Example:

```text id="a3"
[1, 3, 8, 12, 9, 5, 2]
```

## Idea

* Find peak element using Binary Search
* Search left side (ascending)
* Search right side (descending)

---

# 4. Infinite Sorted Array

An array whose size is not known.

## Idea

* Use exponential search to find range
* Then apply Binary Search

Steps:

* Start with small range
* Double the range until target is within bounds

---

# 5. Peak Element Problem

A peak element is greater than its neighbors.

Example:

```text id="a4"
[1, 3, 20, 4, 1]
```

## Idea

Use Binary Search:

* If mid is peak → return
* If left side is greater → move left
* Else move right

---

# Key Idea

All special array problems rely on:

* Binary Search modification
* Identifying array structure
* Reducing search space intelligently

---

# Applications

* Competitive programming
* Interview problem solving
* Optimization problems
* Search in rotated datasets
* Peak finding problems

---

# Advantages

* Efficient searching in non-standard arrays
* Reduces brute force O(n) to O(log n)
* Powerful pattern-based technique
* Frequently asked in interviews

---

# Limitations

* Requires pattern recognition
* More complex than normal binary search
* Easy to make boundary mistakes
* Different logic for each variation

---

# Complexity Analysis

## Time Complexity

| Problem Type          | Complexity |
| --------------------- | ---------- |
| Rotated Array Search  | O(log n)   |
| Nearly Sorted Array   | O(log n)   |
| Bitonic Array         | O(log n)   |
| Infinite Array Search | O(log n)   |

---

## Space Complexity

| Type      | Space                 |
| --------- | --------------------- |
| Iterative | O(1)                  |
| Recursive | O(log n) (rare cases) |

---

# Interview Questions

## 1. What is a Rotated Sorted Array?

**Answer:**
It is a sorted array that has been rotated at some pivot point, creating two sorted subarrays.

---

## 2. How do you search in a Rotated Sorted Array?

**Answer:**
By identifying the sorted half and checking whether the target lies in that half, then applying binary search accordingly.

---

## 3. What is a Bitonic Array?

**Answer:**
A Bitonic Array is an array that increases first and then decreases.

---

## 4. How do you find a peak element?

**Answer:**
Using Binary Search by comparing mid with its neighbors and moving towards the larger side.

---

## 5. What is an Infinite Sorted Array?

**Answer:**
An array where the size is unknown, so we first find a range using exponential search and then apply binary search.

---

## 6. Why is Binary Search used in special arrays?

**Answer:**
Because even though the array is not fully sorted, its structure allows reducing the search space efficiently.

---

## 7. What is the time complexity of searching in special arrays?

**Answer:**
Most problems are solved in **O(log n)** using modified binary search.

---

## 8. What is the main challenge in these problems?

**Answer:**
Identifying the correct pattern and deciding which half of the array to search.

---

## 9. Can linear search be used in special arrays?

**Answer:**
Yes, but it is inefficient (O(n)) compared to optimized binary search approaches.

---

## 10. What is the key technique used in all special array problems?

**Answer:**
Modified Binary Search based on array structure and properties.
