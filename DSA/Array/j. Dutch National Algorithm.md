# Dutch National Flag Algorithm

## Definition

The **Dutch National Flag Algorithm** is a sorting technique used to arrange elements into three distinct groups in a single pass. It is most commonly used to sort arrays containing only three unique values (for example: 0, 1, 2).

It was proposed by **Edsger Dijkstra** and is widely used in coding interviews for in-place array partitioning problems.

---

# Problem Type

This algorithm is used when we need to:

* Sort an array with 3 distinct elements
* Partition data into 3 categories
* Perform in-place sorting efficiently

Example:

```text id="a1"
Input:  [2, 0, 2, 1, 1, 0]
Output: [0, 0, 1, 1, 2, 2]
```

---

# Idea Behind the Algorithm

We use **three pointers**:

* `low` → boundary of 0s
* `mid` → current element
* `high` → boundary of 2s

We divide the array into 3 parts:

* `0 to low-1` → all 0s
* `low to mid-1` → all 1s
* `high+1 to end` → all 2s

---

# Algorithm Steps

1. Initialize:

   * `low = 0`
   * `mid = 0`
   * `high = n - 1`

2. Traverse while `mid <= high`

3. Conditions:

   * If `arr[mid] == 0`:

     * swap `arr[low]` and `arr[mid]`
     * increment `low` and `mid`
   * If `arr[mid] == 1`:

     * increment `mid`
   * If `arr[mid] == 2`:

     * swap `arr[mid]` and `arr[high]`
     * decrement `high`

---

# Example Walkthrough

Array:

```text id="a2"
[2, 0, 2, 1, 1, 0]
```

Steps:

```text id="a3"
Initial: low = 0, mid = 0, high = 5

Step 1: 2 → swap with high → [0, 0, 2, 1, 1, 2]
Step 2: 0 → swap with low → [0, 0, 2, 1, 1, 2]
Step 3: 2 → swap with high → [0, 0, 1, 1, 2, 2]
```

Final Output:

```text id="a4"
[0, 0, 1, 1, 2, 2]
```

---

# Key Idea

The array is partitioned into three regions dynamically:

* Left side → 0s
* Middle → 1s
* Right side → 2s

All elements are processed in a **single traversal**.

---

# Applications

* Sorting 0s, 1s, 2s problem
* Partitioning arrays
* Segregating positive, negative, and zero values (variations)
* Dutch Flag partition problems in interviews
* QuickSort partitioning logic (conceptually related)

---

# Advantages

* In-place sorting (no extra space)
* Single pass solution
* Very efficient for small fixed-value datasets
* Reduces complexity from O(n log n) to O(n)

---

# Limitations

* Works only for a fixed number of categories (mainly 3)
* Not suitable for general sorting problems
* Requires careful pointer handling

---

# Complexity Analysis

## Time Complexity

```text id="t1"
O(n)
```

Each element is processed at most once.

---

## Space Complexity

```text id="s1"
O(1)
```

Sorting is done in-place using only pointers.

---

# Interview Questions

## 1. What is the Dutch National Flag Algorithm?

**Answer:**
It is an in-place algorithm used to sort an array containing three distinct values (like 0, 1, and 2) in a single traversal.

---

## 2. Why is it called Dutch National Flag Algorithm?

**Answer:**
Because the problem resembles sorting colors of the Dutch flag (red, white, blue) into three partitions.

---

## 3. What are the pointers used in this algorithm?

**Answer:**

* `low` → tracks 0s region
* `mid` → current element
* `high` → tracks 2s region

---

## 4. What is the time complexity of this algorithm?

**Answer:**
It runs in **O(n)** because each element is processed at most once.

---

## 5. What is the space complexity?

**Answer:**
It uses **O(1)** extra space since sorting is done in-place.

---

## 6. Why is this algorithm efficient?

**Answer:**
Because it sorts the array in a single pass without using extra memory or multiple iterations.

---

## 7. Can this algorithm be used for more than 3 values?

**Answer:**
No, it is designed specifically for 3 distinct categories. For more values, other sorting techniques are needed.

---

## 8. What is the main idea behind this algorithm?

**Answer:**
To partition the array into three regions using three pointers while processing each element only once.

---

## 9. How is it related to QuickSort?

**Answer:**
It is conceptually similar to the partition step in QuickSort, where elements are rearranged around a pivot.

---

## 10. Where is this algorithm used in interviews?

**Answer:**
It is commonly asked in array manipulation problems, especially:

* Sorting 0s, 1s, 2s
* Partition-based questions
* In-place sorting problems
