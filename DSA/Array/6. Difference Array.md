# Difference Array

## Definition

A **Difference Array** is a technique used to perform **range update operations efficiently** on an array. Instead of updating each element in a range individually, we update only two positions, which makes the operation much faster.

This technique is especially useful when there are **multiple range update queries**.

---

# Idea Behind Difference Array

Instead of storing the actual array values, we store the difference between consecutive elements.

For an array `A`, we create a difference array `D` such that:

```text id="d1"
D[i] = A[i] - A[i - 1]
```

Where:

* `D[0] = A[0]`

---

# Construction

Given an array:

```text id="a1"
A = [2, 4, 6, 8, 10]
```

Difference array:

```text id="a2"
D = [2, 2, 2, 2, 2]
```

Explanation:

```text id="a3"
D[0] = 2
D[1] = 4 - 2 = 2
D[2] = 6 - 4 = 2
D[3] = 8 - 6 = 2
D[4] = 10 - 8 = 2
```

---

# Range Update Operation

Instead of updating all elements from index `L` to `R`, we do:

### Steps:

1. Add `val` at index `L`
2. Subtract `val` at index `R + 1` (if exists)

### Formula:

```text id="u1"
D[L] += val
D[R + 1] -= val
```

---

# Example

Initial array:

```text id="b1"
A = [0, 0, 0, 0, 0]
```

Queries:

* Add 5 from index 1 to 3

Difference array update:

```text id="b2"
D[1] += 5
D[4] -= 5
```

Final prefix reconstruction:

```text id="b3"
[0, 5, 5, 5, 0]
```

---

# Rebuilding Final Array

After all updates, we reconstruct the final array using prefix sum:

```text id="r1"
A[i] = A[i - 1] + D[i]
```

---

# Applications

* Range update queries
* Competitive programming problems
* Frequency manipulation
* Interval addition problems
* Efficient array modification

---

# Advantages

* Range updates in **O(1)** time
* Very efficient for multiple queries
* Reduces repeated updates

---

# Limitations

* Requires final reconstruction
* Not useful for frequent queries and updates mixed together
* Extra space required for difference array

---

# Complexity Analysis

## Time Complexity

| Operation           | Complexity |
| ------------------- | ---------- |
| Single Range Update | O(1)       |
| Reconstruct Array   | O(n)       |
| Multiple Updates    | O(k)       |

---

## Space Complexity

```text id="s1"
O(n)
```

Extra array is used to store differences.

---

# Interview Questions

## 1. What is a Difference Array?

**Answer:**
A Difference Array is a technique used to perform range updates efficiently by updating only two positions instead of updating every element in the range.

---

## 2. Why do we use a Difference Array?

**Answer:**
It reduces the time complexity of range update operations from **O(n)** to **O(1)** per update.

---

## 3. What is the formula for range update in a Difference Array?

**Answer:**

```text id="f1"
D[L] += val
D[R + 1] -= val
```

---

## 4. How do you construct the original array from a Difference Array?

**Answer:**
By taking the prefix sum of the difference array:

```text id="f2"
A[i] = A[i - 1] + D[i]
```

---

## 5. What is the time complexity of applying a range update?

**Answer:**
Each update takes **O(1)** time.

---

## 6. What is the time complexity of rebuilding the array?

**Answer:**
Reconstructing the final array takes **O(n)** time.

---

## 7. When should we use a Difference Array?

**Answer:**
When there are multiple range update operations and we need an efficient way to handle them.

---

## 8. What is the main advantage of Difference Array?

**Answer:**
It allows fast range updates in constant time, making it highly efficient for large datasets.

---

## 9. What is the limitation of Difference Array?

**Answer:**
It requires rebuilding the array after updates and is not suitable when frequent queries and updates are mixed.

---

## 10. Difference between Prefix Sum and Difference Array?

**Answer:**

| Prefix Sum                 | Difference Array              |
| -------------------------- | ----------------------------- |
| Used for range sum queries | Used for range update queries |
| Preprocess once            | Update efficiently            |
| O(1) query time            | O(1) update time              |
| Stores cumulative sums     | Stores differences            |
