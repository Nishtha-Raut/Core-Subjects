# Time & Space Complexity of Array Operations

## Definition

Every operation performed on an array requires a certain amount of **time** and **memory**. The efficiency of these operations is measured using **Time Complexity** and **Space Complexity**.

Understanding these complexities helps in selecting the most efficient algorithm and is one of the most frequently asked topics in coding interviews.

---

# Time Complexity

**Time Complexity** measures how the execution time of an operation grows as the number of elements (`n`) in the array increases.

Different array operations have different time complexities depending on whether they require direct access, traversal, or shifting of elements.

---

# Space Complexity

**Space Complexity** measures the amount of extra memory required by an operation.

Most basic array operations use **O(1)** extra space because they modify the existing array without creating another array.

However, some algorithms may require additional arrays, increasing the space complexity.

---

# Complexity of Array Operations

## 1. Access

Accessing an element using its index is the fastest array operation because the memory address is calculated directly.

**Time Complexity:** `O(1)`

**Space Complexity:** `O(1)`

---

## 2. Traversal

Traversal visits every element exactly once.

**Time Complexity:** `O(n)`

**Space Complexity:** `O(1)`

---

## 3. Linear Search

Linear Search compares the target element with each array element until it is found or the array ends.

**Best Case:** `O(1)`

**Average Case:** `O(n)`

**Worst Case:** `O(n)`

**Space Complexity:** `O(1)`

---

## 4. Binary Search

Binary Search repeatedly divides the search space into two halves.

It works **only on sorted arrays**.

**Best Case:** `O(1)`

**Average Case:** `O(log n)`

**Worst Case:** `O(log n)`

**Space Complexity:**

* Iterative: `O(1)`
* Recursive: `O(log n)` (due to recursion stack)

---

## 5. Insertion

Insertion depends on the position where the new element is inserted.

* Beginning → Elements must be shifted.
* Middle → Elements must be shifted.
* End → No shifting (if space is available).

### Time Complexity

| Position  | Complexity                  |
| --------- | --------------------------- |
| Beginning | O(n)                        |
| Middle    | O(n)                        |
| End       | O(1) *(if space available)* |

**Space Complexity:** `O(1)`

---

## 6. Deletion

After deleting an element, all remaining elements must be shifted left to fill the gap.

**Time Complexity:** `O(n)`

**Space Complexity:** `O(1)`

---

## 7. Updating

Updating replaces the value at a known index.

Since the index is already known, no traversal or shifting is required.

**Time Complexity:** `O(1)`

**Space Complexity:** `O(1)`

---

# Summary Table

| Operation             | Best Case | Average Case | Worst Case                  | Space              |
| --------------------- | --------- | ------------ | --------------------------- | ------------------ |
| Access                | O(1)      | O(1)         | O(1)                        | O(1)               |
| Traversal             | O(n)      | O(n)         | O(n)                        | O(1)               |
| Linear Search         | O(1)      | O(n)         | O(n)                        | O(1)               |
| Binary Search         | O(1)      | O(log n)     | O(log n)                    | O(1) *(Iterative)* |
| Insertion (Beginning) | O(n)      | O(n)         | O(n)                        | O(1)               |
| Insertion (Middle)    | O(n)      | O(n)         | O(n)                        | O(1)               |
| Insertion (End)       | O(1)      | O(1)         | O(1) *(if space available)* | O(1)               |
| Deletion              | O(n)      | O(n)         | O(n)                        | O(1)               |
| Updating              | O(1)      | O(1)         | O(1)                        | O(1)               |

---

# Interview Questions

## 1. Why is accessing an array element O(1)?

**Answer:**
Because the memory address of an element is calculated directly using its index, allowing immediate access without traversing the array.

---

## 2. Why is traversal O(n)?

**Answer:**
Traversal visits every element exactly once, so the execution time increases linearly with the number of elements.

---

## 3. Why is insertion at the beginning O(n)?

**Answer:**
Because all existing elements must be shifted one position to the right before inserting the new element.

---

## 4. Why is deletion O(n)?

**Answer:**
After deleting an element, the remaining elements must be shifted left to maintain contiguous memory.

---

## 5. Why is updating O(1)?

**Answer:**
Updating uses the element's index to directly replace its value without any traversal or shifting.

---

## 6. Why is Binary Search faster than Linear Search?

**Answer:**
Binary Search eliminates half of the search space in every step, giving it a time complexity of **O(log n)**, whereas Linear Search checks elements one by one, resulting in **O(n)** time complexity.

---

## 7. Which array operations are constant time?

**Answer:**

* Access
* Updating
* Insertion at the end *(when space is available)*

These operations generally take **O(1)** time.

---

## 8. Which array operations require shifting elements?

**Answer:**

* Insertion at the beginning
* Insertion in the middle
* Deletion

These operations require shifting elements and therefore have **O(n)** time complexity.

---

## 9. Does every array operation require extra memory?

**Answer:**
No. Most basic array operations modify the existing array and require only **O(1)** extra space.

---

## 10. Which operation is the most expensive in an array?

**Answer:**
Insertion and deletion at the beginning or middle are the most expensive because they require shifting multiple elements, resulting in **O(n)** time complexity.
