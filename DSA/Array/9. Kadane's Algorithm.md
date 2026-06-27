# Kadane’s Algorithm

## Definition

**Kadane’s Algorithm** is an efficient technique used to find the **maximum sum subarray** in a given array.

A subarray is a contiguous part of an array, and the goal is to find the subarray with the highest possible sum.

Kadane’s Algorithm solves this problem in **O(n)** time using a greedy approach.

---

# Problem Type

This algorithm is used when we need to:

* Find maximum subarray sum
* Optimize brute force O(n²) solutions
* Handle positive and negative numbers in arrays

---

# Key Idea

At every index, we decide:

* Either extend the previous subarray
* Or start a new subarray from the current element

We maintain:

* `currentSum` → best sum ending at current position
* `maxSum` → best sum found so far

---

# Algorithm Steps

1. Initialize:

   * `currentSum = 0`
   * `maxSum = Integer.MIN_VALUE`

2. Traverse the array:

   * Add current element to `currentSum`
   * Update `maxSum = max(maxSum, currentSum)`
   * If `currentSum < 0`, reset it to 0

3. Return `maxSum`

---

# Example

Array:

```text id="a1"
[-2, 1, -3, 4, -1, 2, 1, -5, 4]
```

Steps:

```text id="a2"
currentSum = 0, maxSum = -∞

-2 → currentSum = -2 → reset to 0 → maxSum = -2
1  → currentSum = 1  → maxSum = 1
-3 → currentSum = -2 → reset to 0
4  → currentSum = 4  → maxSum = 4
-1 → currentSum = 3  → maxSum = 4
2  → currentSum = 5  → maxSum = 5
1  → currentSum = 6  → maxSum = 6
-5 → currentSum = 1  → maxSum = 6
4  → currentSum = 5  → maxSum = 6
```

Final Answer:

```text id="a3"
6
```

Subarray:

```text id="a4"
[4, -1, 2, 1]
```

---

# Key Idea

If the running sum becomes negative, we discard it because it will only reduce future sums.

---

# Applications

* Maximum subarray sum problem
* Stock profit problems (variation)
* Dynamic programming optimization
* Competitive programming
* Real-world profit/loss analysis

---

# Advantages

* Linear time solution O(n)
* Simple and efficient
* No extra space required
* Works for both positive and negative numbers

---

# Limitations

* Only finds contiguous subarray
* Does not handle non-contiguous selections
* Requires slight modification for variations

---

# Complexity Analysis

## Time Complexity

```text id="t1"
O(n)
```

Single traversal of the array.

---

## Space Complexity

```text id="s1"
O(1)
```

Only a few variables are used.

---

# Interview Questions

## 1. What is Kadane’s Algorithm?

**Answer:**
Kadane’s Algorithm is used to find the maximum sum subarray in an array using a linear time greedy approach.

---

## 2. What is the key idea of Kadane’s Algorithm?

**Answer:**
At each step, decide whether to extend the current subarray or start a new one.

---

## 3. Why do we reset currentSum to 0?

**Answer:**
Because a negative sum will reduce the future subarray sum, so it is better to start fresh.

---

## 4. What is the time complexity of Kadane’s Algorithm?

**Answer:**
It runs in **O(n)** time since it scans the array once.

---

## 5. What is the space complexity?

**Answer:**
It uses **O(1)** space as no extra data structures are needed.

---

## 6. What is the difference between brute force and Kadane’s Algorithm?

**Answer:**

| Approach           | Time Complexity |
| ------------------ | --------------- |
| Brute Force        | O(n²)           |
| Kadane’s Algorithm | O(n)            |

---

## 7. Can Kadane’s Algorithm handle negative numbers?

**Answer:**
Yes, it works for arrays containing both positive and negative numbers.

---

## 8. What is the condition to reset currentSum?

**Answer:**
If `currentSum < 0`, it is reset to 0.

---

## 9. What kind of problems use Kadane’s Algorithm?

**Answer:**
Problems involving maximum subarray sum, profit optimization, and contiguous sequence optimization.

---

## 10. Does Kadane’s Algorithm work for non-contiguous subarrays?

**Answer:**
No, it only works for contiguous subarrays.
