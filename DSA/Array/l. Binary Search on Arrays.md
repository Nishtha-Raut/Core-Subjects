# Binary Search on Arrays

## Definition

**Binary Search** is an efficient searching technique used on **sorted arrays**. It works by repeatedly dividing the search space into two halves and eliminating the half where the target cannot exist.

It reduces the time complexity from **O(n)** (linear search) to **O(log n)**.

---

# Prerequisite

Binary Search works only when:

* The array is **sorted**
* Elements are comparable

---

# Key Idea

Instead of checking each element one by one, we:

* Compare the middle element with the target
* Decide whether to go left or right
* Reduce the search space by half each time

---

# Algorithm Steps (Iterative)

1. Initialize:

   * `low = 0`
   * `high = n - 1`

2. While `low <= high`:

   * Find `mid = (low + high) / 2`
   * If `arr[mid] == target` → return index
   * If `target < arr[mid]` → search left half
   * Else → search right half

3. If not found → return `-1`

---

# Example

Array:

```text id="a1"
[1, 3, 5, 7, 9, 11]
```

Target = `7`

Steps:

```text id="a2"
low=0, high=5 → mid=2 → 5 < 7 → move right
low=3, high=5 → mid=4 → 9 > 7 → move left
low=3, high=3 → mid=3 → 7 found
```

---

# Recursive Binary Search

Binary Search can also be implemented using recursion:

* Base case: low > high
* Recursive calls on left or right half

---

# Variations of Binary Search

## 1. First Occurrence

Find the first index of a duplicate element.

## 2. Last Occurrence

Find the last index of a duplicate element.

## 3. Lower Bound

First index where element is ≥ target.

## 4. Upper Bound

First index where element is > target.

---

# Binary Search on Answer

Used when:

* We are searching for an **optimal answer**
* We apply binary search on a range of values instead of array

Example problems:

* Minimum maximum allocation
* Koko eating bananas
* Capacity problems

---

# Applications

* Searching in sorted arrays
* Finding boundaries (first/last occurrence)
* Optimization problems
* Searching in rotated arrays
* Real-world search systems
* Competitive programming

---

# Advantages

* Very fast searching (O(log n))
* Reduces search space exponentially
* Efficient for large datasets
* Widely used in interview problems

---

# Limitations

* Works only on sorted data
* Requires careful implementation
* Not suitable for unsorted arrays
* Slightly complex for variations

---

# Complexity Analysis

## Time Complexity

| Case         | Complexity |
| ------------ | ---------- |
| Best Case    | O(1)       |
| Average Case | O(log n)   |
| Worst Case   | O(log n)   |

---

## Space Complexity

| Implementation | Space    |
| -------------- | -------- |
| Iterative      | O(1)     |
| Recursive      | O(log n) |

(recursion stack space)

---

# Interview Questions

## 1. What is Binary Search?

**Answer:**
Binary Search is a searching technique that works on sorted arrays by dividing the search space into two halves repeatedly.

---

## 2. Why is Binary Search faster than Linear Search?

**Answer:**
Because it eliminates half of the search space in each step, reducing time complexity to **O(log n)**.

---

## 3. What is the prerequisite for Binary Search?

**Answer:**
The array must be sorted.

---

## 4. What is the time complexity of Binary Search?

**Answer:**

* Best Case: O(1)
* Average Case: O(log n)
* Worst Case: O(log n)

---

## 5. What is the space complexity of Binary Search?

**Answer:**

* Iterative: O(1)
* Recursive: O(log n)

---

## 6. What is the difference between Linear Search and Binary Search?

**Answer:**

| Linear Search            | Binary Search               |
| ------------------------ | --------------------------- |
| Works on unsorted arrays | Works only on sorted arrays |
| O(n)                     | O(log n)                    |
| Checks each element      | Divides search space        |

---

## 7. What is mid calculation in Binary Search?

**Answer:**

```text id="m1"
mid = (low + high) / 2
```

(Optimized form: `low + (high - low) / 2`)

---

## 8. What are common variations of Binary Search?

**Answer:**

* First occurrence
* Last occurrence
* Lower bound
* Upper bound
* Binary search on answer

---

## 9. What is Binary Search on Answer?

**Answer:**
It is a technique where binary search is applied on a range of possible answers instead of an array.

---

## 10. Where is Binary Search used in real life?

**Answer:**
It is used in searching systems, databases, optimization problems, and large-scale sorted data retrieval systems.
