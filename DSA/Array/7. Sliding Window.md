# Sliding Window Technique

## Definition

The **Sliding Window Technique** is an optimization method used to reduce the time complexity of problems involving **subarrays or substrings**. Instead of recalculating results for every possible window, we maintain a “window” that moves through the array efficiently.

It is mainly used to solve problems involving:

* Contiguous subarrays
* Fixed or variable size windows
* Optimization of brute force solutions

---

# Types of Sliding Window

## 1. Fixed Size Window

In this type, the size of the window is already given.

We:

* Maintain a window of size `k`
* Slide it one element at a time
* Update the result efficiently

### Example Problems:

* Maximum sum of subarray of size K
* First negative integer in every window

---

## 2. Variable Size Window

In this type, the size of the window is not fixed.

We:

* Expand the window to satisfy a condition
* Shrink it when the condition breaks
* Maintain an optimal valid window

### Example Problems:

* Longest substring without repeating characters
* Smallest subarray with sum ≥ K

---

# Idea Behind Sliding Window

Instead of recalculating values for every subarray, we:

* Add the new element entering the window
* Remove the element leaving the window

This avoids repeated computations and improves efficiency.

---

# Fixed Size Window Example

### Problem: Maximum sum of subarray of size K

Array:

```text id="a1"
[2, 1, 5, 1, 3, 2]
```

Window size = 3

### Step 1: First window

```text id="a2"
2 + 1 + 5 = 8
```

### Step 2: Slide window

Remove 2, add 1 → 1 + 5 + 1 = 7
Remove 1, add 3 → 5 + 1 + 3 = 9
Remove 5, add 2 → 1 + 3 + 2 = 6

### Result:

```text id="a3"
Maximum Sum = 9
```

---

# Variable Size Window Example

### Problem: Longest substring without repeating characters

Idea:

* Expand right pointer
* If duplicate found → move left pointer

Example:

```text id="b1"
"abcabcbb"
```

Longest valid substring:

```text id="b2"
"abc"
```

Length = 3

---

# Key Idea

We use two pointers:

* `left` → start of window
* `right` → end of window

We adjust these pointers to maintain a valid window.

---

# Applications

* Subarray problems
* String problems
* Maximum/minimum subarray sum
* Longest/shortest substring problems
* Competitive programming
* Real-time data stream processing

---

# Advantages

* Reduces brute force O(n²) to O(n)
* Efficient for contiguous problems
* Simple and powerful pattern
* Widely used in interviews

---

# Limitations

* Only works on **contiguous data**
* Not suitable for non-continuous problems
* Requires careful handling of conditions in variable windows

---

# Complexity Analysis

## Time Complexity

| Type            | Complexity |
| --------------- | ---------- |
| Fixed Window    | O(n)       |
| Variable Window | O(n)       |

Each element is processed at most twice (once added, once removed).

---

## Space Complexity

```text id="s1"
O(1)
```

(usually only pointers and variables are used)

---

# Interview Questions

## 1. What is the Sliding Window Technique?

**Answer:**
It is an optimization technique used to reduce the time complexity of subarray or substring problems by maintaining a moving window instead of recomputing results.

---

## 2. When do we use Sliding Window?

**Answer:**
We use it when dealing with problems involving:

* Subarrays
* Substrings
* Contiguous sequences
* Optimization of brute force solutions

---

## 3. What is the difference between Fixed and Variable Sliding Window?

**Answer:**

| Fixed Window             | Variable Window                   |
| ------------------------ | --------------------------------- |
| Window size is constant  | Window size changes dynamically   |
| Easier to implement      | Slightly complex logic            |
| Used for k-size problems | Used for condition-based problems |

---

## 4. Why is Sliding Window efficient?

**Answer:**
Because each element is processed at most twice (once added and once removed), making the time complexity linear.

---

## 5. What is the time complexity of Sliding Window?

**Answer:**
Both fixed and variable sliding window approaches typically run in **O(n)** time.

---

## 6. What is the main idea behind Sliding Window?

**Answer:**
To avoid recalculating results for every subarray by maintaining a running result as the window slides.

---

## 7. Can Sliding Window be used for all array problems?

**Answer:**
No. It only works for problems involving contiguous subarrays or substrings.

---

## 8. What are the two pointers in Sliding Window?

**Answer:**

* Left pointer (start of window)
* Right pointer (end of window)

They define the current active window.

---

## 9. What is the space complexity of Sliding Window?

**Answer:**
Usually **O(1)**, since only a few variables are used.

---

## 10. Why is Sliding Window better than brute force?

**Answer:**
Because brute force recalculates results for every subarray (O(n²)), while Sliding Window reuses previous computations and reduces it to O(n).
