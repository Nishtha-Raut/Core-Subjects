# Divide and Conquer Algorithm

## Definition

**Divide and Conquer** is an algorithmic paradigm where a problem is:

1. **Divided** into smaller subproblems
2. **Conquered** by solving subproblems recursively
3. **Combined** to get the final result

It is widely used in efficient algorithms that reduce time complexity significantly.

---

# Key Idea

Instead of solving a large problem directly, we:

* Break it into smaller parts
* Solve each part independently
* Merge results to form the final solution

---

# Steps of Divide and Conquer

## 1. Divide

Split the problem into smaller subproblems of the same type.

## 2. Conquer

Solve each subproblem recursively.

## 3. Combine

Merge solutions of subproblems to get final answer.

---

# Example (General Idea)

Array:

```text id="a1"
[8, 3, 5, 4, 7, 6, 1, 2]
```

We divide it into halves repeatedly:

```text id="a2"
[8, 3, 5, 4] + [7, 6, 1, 2]
→ further split
→ solve individually
→ merge back sorted
```

---

# Applications in Data Structures & Algorithms

Divide and Conquer is used in many important algorithms:

---

## 1. Merge Sort

* Divides array into halves
* Sorts each half
* Merges sorted halves

Time Complexity:

```text id="m1"
O(n log n)
```

---

## 2. Quick Sort

* Picks a pivot
* Partitions array into left and right
* Recursively sorts partitions

Time Complexity:

* Best: O(n log n)
* Worst: O(n²)

---

## 3. Binary Search

* Divides search space into half
* Eliminates one half each step

Time Complexity:

```text id="b1"
O(log n)
```

---

## 4. Maximum/Minimum Problems

* Divide array into halves
* Find min/max in each half
* Combine results

---

## 5. Strassen’s Matrix Multiplication (Advanced)

* Used in matrix multiplication optimization
* Reduces complexity compared to naive approach

---

# Advantages

* Reduces time complexity significantly
* Breaks complex problems into simpler parts
* Easy to apply recursively
* Efficient for large datasets

---

# Limitations

* Recursive calls increase stack usage
* Can be complex to implement
* Overhead of merging results
* Not always optimal for small datasets

---

# Complexity Analysis

## Time Complexity

Depends on recurrence relation:

General form:

```text id="t1"
T(n) = aT(n/b) + O(n^k)
```

Examples:

* Merge Sort → O(n log n)
* Binary Search → O(log n)
* Quick Sort → O(n log n) (average)

---

## Space Complexity

* Usually **O(log n)** due to recursion stack
* Merge Sort uses extra space O(n)

---

# Interview Questions

## 1. What is Divide and Conquer?

**Answer:**
Divide and Conquer is a technique where a problem is divided into smaller subproblems, solved recursively, and combined to form the final solution.

---

## 2. What are the steps of Divide and Conquer?

**Answer:**

* Divide the problem
* Conquer subproblems recursively
* Combine results

---

## 3. Which algorithms use Divide and Conquer?

**Answer:**

* Merge Sort
* Quick Sort
* Binary Search
* Strassen’s Matrix Multiplication

---

## 4. Why is Divide and Conquer efficient?

**Answer:**
Because it breaks large problems into smaller parts, reducing overall time complexity.

---

## 5. What is the time complexity of Merge Sort?

**Answer:**
O(n log n)

---

## 6. What is the time complexity of Binary Search?

**Answer:**
O(log n)

---

## 7. What is the space complexity of Divide and Conquer?

**Answer:**
Usually O(log n) due to recursion stack.

---

## 8. What is the difference between Divide and Conquer and Dynamic Programming?

**Answer:**

| Divide and Conquer      | Dynamic Programming   |
| ----------------------- | --------------------- |
| Subproblems independent | Subproblems overlap   |
| No memoization          | Uses memoization      |
| Example: Merge Sort     | Example: Fibonacci DP |

---

## 9. What is the main disadvantage of Divide and Conquer?

**Answer:**
It uses recursion, which can increase stack space and overhead.

---

## 10. Why is Merge Sort called Divide and Conquer?

**Answer:**
Because it divides the array into halves, sorts them recursively, and then merges them.
