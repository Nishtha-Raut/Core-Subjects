# Two Pointer Technique

## Definition

The **Two Pointer Technique** is an algorithmic approach where two pointers are used to traverse a data structure (usually an array or string) in order to solve problems efficiently.

Instead of using nested loops (brute force), we use two indices that move in a controlled way to reduce time complexity.

It is widely used in problems involving:

* Searching
* Sorting-based problems
* Subarrays and pairs
* Optimization problems

---

# Types of Two Pointer Technique

## 1. Opposite Direction Pointers

Two pointers start from opposite ends of the array and move towards each other.

### Example Use Cases:

* Checking if array is palindrome
* Two sum in sorted array
* Reversing array

### Idea:

* `left = 0`
* `right = n - 1`
* Move pointers based on condition

---

## 2. Same Direction Pointers

Both pointers move in the same direction, usually at different speeds or conditions.

### Example Use Cases:

* Removing duplicates
* Partition problems
* Sliding window (related concept)

### Idea:

* `slow` and `fast` pointers
* Used to track valid positions or conditions

---

## 3. Fast and Slow Pointer (Floyd’s Technique)

A special case where one pointer moves faster than the other.

### Example Use Cases:

* Detect cycle in linked list
* Find middle of linked list
* Cycle detection problems

---

# How Two Pointers Work

Instead of checking all pairs (O(n²)), we reduce it to **O(n)** or **O(n log n)** depending on sorting.

We move pointers based on conditions:

* If condition is satisfied → move one pointer
* If not → adjust other pointer

---

# Example 1: Two Sum (Sorted Array)

Array:

```text id="a1"
[1, 2, 3, 4, 6]
```

Target = 6

Steps:

```text id="a2"
left = 0 (1), right = 4 (6)
1 + 6 = 7 → decrease right
1 + 4 = 5 → increase left
2 + 4 = 6 → found
```

---

# Example 2: Palindrome Check

String:

```text id="b1"
"madam"
```

Steps:

* Compare left and right characters
* Move inward if equal
* Stop if mismatch found

---

# Key Idea

Two pointers help reduce unnecessary comparisons by:

* Eliminating nested loops
* Leveraging sorted order or structure
* Moving intelligently instead of brute force scanning

---

# Applications

* Pair sum problems
* Array reversal
* Partitioning arrays
* String palindrome checking
* Removing duplicates
* Linked list problems
* Searching in sorted arrays

---

# Advantages

* Reduces O(n²) to O(n)
* Simple and efficient
* Works well on sorted data
* Reduces memory usage (no extra structures needed)

---

# Limitations

* Works best on sorted arrays or specific patterns
* Requires careful pointer movement logic
* Not suitable for random access logic problems

---

# Complexity Analysis

## Time Complexity

| Case                | Complexity |
| ------------------- | ---------- |
| Opposite Direction  | O(n)       |
| Same Direction      | O(n)       |
| Fast & Slow Pointer | O(n)       |

Each element is processed at most once or twice.

---

## Space Complexity

```text id="s1"
O(1)
```

Only pointers are used, no extra data structures.

---

# Interview Questions

## 1. What is the Two Pointer Technique?

**Answer:**
It is an algorithmic technique where two pointers are used to traverse a data structure to reduce time complexity and avoid nested loops.

---

## 2. When do we use Two Pointer Technique?

**Answer:**
It is used in:

* Sorted arrays
* Searching pairs
* Palindromes
* Subarray problems
* Linked list problems

---

## 3. What are the types of Two Pointer Technique?

**Answer:**

* Opposite direction pointers
* Same direction pointers
* Fast and slow pointers

---

## 4. Why is Two Pointer Technique efficient?

**Answer:**
Because it reduces nested loop solutions (O(n²)) into linear time O(n) by intelligently moving pointers.

---

## 5. What is the time complexity of Two Pointer Technique?

**Answer:**
Most problems solved using this technique run in **O(n)** time.

---

## 6. What is the space complexity of Two Pointer Technique?

**Answer:**
Usually **O(1)** because no extra data structures are used.

---

## 7. What is the difference between Sliding Window and Two Pointer?

**Answer:**

| Two Pointer            | Sliding Window                   |
| ---------------------- | -------------------------------- |
| General technique      | Specialized form of two pointers |
| Can move independently | Maintains a window               |
| Used in many problems  | Used in subarray/substrings      |

---

## 8. What is Fast and Slow Pointer used for?

**Answer:**
It is used for cycle detection, finding middle elements, and linked list problems.

---

## 9. Can Two Pointer be used on unsorted arrays?

**Answer:**
Yes, but sorting is often required to apply it effectively.

---

## 10. Why is sorting important for Two Pointer Technique?

**Answer:**
Sorting helps establish order, allowing pointers to move logically based on comparisons.
