# Prefix Sum

## Definition

**Prefix Sum** is a technique used to preprocess an array so that the sum of any subarray can be calculated efficiently.

Instead of calculating the sum repeatedly for different ranges, we first create a **Prefix Sum Array**, where each element stores the cumulative sum from the beginning of the array up to that index.

This technique is widely used in range sum queries and is one of the most common interview topics.

---

# Prefix Sum Array

A Prefix Sum Array stores the cumulative sum of elements.

For an array:

```text
[2, 4, 6, 8, 10]
```

The Prefix Sum Array is:

```text
[2, 6, 12, 20, 30]
```

Explanation:

```text
Prefix[0] = 2
Prefix[1] = 2 + 4 = 6
Prefix[2] = 2 + 4 + 6 = 12
Prefix[3] = 20
Prefix[4] = 30
```

---

# Construction

To build a Prefix Sum Array:

1. Store the first element as it is.
2. Add the current element to the previous prefix sum.
3. Repeat until the end of the array.

Formula:

```text
Prefix[i] = Prefix[i - 1] + Array[i]
```

---

# Range Sum Query

Once the Prefix Sum Array is created, the sum of any subarray can be found in **O(1)** time.

### Formula

If `L` and `R` are the starting and ending indices:

```text
If L == 0

Sum = Prefix[R]

Otherwise

Sum = Prefix[R] - Prefix[L - 1]
```

Example:

Array:

```text
[2, 4, 6, 8, 10]
```

Prefix:

```text
[2, 6, 12, 20, 30]
```

Find the sum from index **1** to **3**:

```text
20 - 2 = 18
```

Subarray:

```text
4 + 6 + 8 = 18
```

---

# Applications

Prefix Sum is commonly used in:

* Range Sum Queries
* Subarray Sum Problems
* Cumulative Frequency Calculation
* Competitive Programming
* Dynamic Programming
* 2D Prefix Sum (Matrix Problems)

---

# Advantages

* Fast range sum queries.
* Simple preprocessing.
* Reduces repeated calculations.
* Improves the efficiency of many array problems.

---

# Limitations

* Requires extra memory for the prefix array.
* Efficient only when the array is not updated frequently.
* If array elements change, the prefix array must be rebuilt.

---

# Complexity Analysis

| Operation              | Time Complexity                               |
| ---------------------- | --------------------------------------------- |
| Build Prefix Sum Array | **O(n)**                                      |
| Single Range Sum Query | **O(1)**                                      |
| Multiple Queries       | **O(q)** *(where q is the number of queries)* |

### Space Complexity

```text
O(n)
```

because an additional prefix array is created.

---

# Interview Questions

## 1. What is a Prefix Sum?

**Answer:**
Prefix Sum is a preprocessing technique in which each element stores the cumulative sum of all previous elements, allowing fast range sum queries.

---

## 2. Why do we use Prefix Sum?

**Answer:**
Prefix Sum is used to answer multiple range sum queries efficiently. Without preprocessing, each query takes **O(n)** time, while Prefix Sum reduces it to **O(1)**.

---

## 3. How do you construct a Prefix Sum Array?

**Answer:**

Use the formula:

```text
Prefix[i] = Prefix[i - 1] + Array[i]
```

The first element remains unchanged.

---

## 4. What is the formula to calculate the sum of a subarray?

**Answer:**

If `L == 0`:

```text
Sum = Prefix[R]
```

Otherwise:

```text
Sum = Prefix[R] - Prefix[L - 1]
```

---

## 5. What is the time complexity of building a Prefix Sum Array?

**Answer:**
Building the Prefix Sum Array requires traversing the array once, so the time complexity is **O(n)**.

---

## 6. What is the time complexity of a range sum query using Prefix Sum?

**Answer:**
After preprocessing, each range sum query can be answered in **O(1)** time.

---

## 7. What is the space complexity of Prefix Sum?

**Answer:**
The Prefix Sum technique requires an additional array of size `n`, so the space complexity is **O(n)**.

---

## 8. What is the main limitation of Prefix Sum?

**Answer:**
If the original array is updated frequently, the Prefix Sum Array must be rebuilt, making it inefficient for dynamic updates.

---

## 9. Can Prefix Sum be used for 2D arrays?

**Answer:**
Yes. A **2D Prefix Sum** is used for efficiently calculating the sum of rectangular submatrices in matrix-based problems.

---

## 10. When should you use Prefix Sum?

**Answer:**
Use Prefix Sum when:

* There are multiple range sum queries.
* The array is mostly static (few or no updates).
* Efficient preprocessing can improve overall performance.
