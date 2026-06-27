# Array Operations

## Definition

Array operations are the basic actions that can be performed on an array to manipulate or access its elements. These operations form the foundation for solving array-based problems and are frequently used in programming and coding interviews.

The most common array operations are:

* Traversal
* Accessing
* Searching
* Insertion
* Deletion
* Updating

---

# 1. Traversal

Traversal is the process of visiting every element of an array exactly once.

It is used when we need to:

* Print all elements.
* Find the sum or average.
* Find the maximum or minimum element.
* Count occurrences of an element.
* Perform an operation on every element.

Since every element is visited once, traversal takes **O(n)** time.

---

# 2. Accessing

Accessing means retrieving an element from an array using its index.

Since arrays store elements in contiguous memory locations, the address of an element can be calculated directly.

Example:

```java
int[] arr = {10, 20, 30, 40};

System.out.println(arr[2]); // 30
```

Accessing an element takes **O(1)** time.

---

# 3. Searching

Searching is the process of finding whether a particular element exists in an array.

The two most common searching techniques are:

### Linear Search

Checks each element one by one until the target element is found.

* Works on both sorted and unsorted arrays.
* Time Complexity: **O(n)**

### Binary Search

Repeatedly divides the search space into two halves.

* Works only on sorted arrays.
* Time Complexity: **O(log n)**

---

# 4. Insertion

Insertion is the process of adding a new element to an array.

Insertion can occur at:

* Beginning
* Middle
* End

If insertion is performed at the beginning or middle, existing elements must be shifted one position to the right.

For static arrays, insertion is possible only if sufficient space is available.

---

# 5. Deletion

Deletion is the process of removing an element from an array.

After deletion, all subsequent elements are shifted one position to the left to maintain contiguous storage.

Deletion is more expensive than updating because it may involve shifting multiple elements.

---

# 6. Updating

Updating means replacing the value of an existing element with a new value.

Example:

```java
int[] arr = {10, 20, 30};

arr[1] = 50;
```

Updated Array:

```text
[10, 50, 30]
```

Updating requires direct index access and therefore takes **O(1)** time.

---

# Complexity Analysis

| Operation                    | Time Complexity                 |
| ---------------------------- | ------------------------------- |
| Access                       | **O(1)**                        |
| Traversal                    | **O(n)**                        |
| Linear Search                | **O(n)**                        |
| Binary Search (Sorted Array) | **O(log n)**                    |
| Insertion at Beginning       | **O(n)**                        |
| Insertion at Middle          | **O(n)**                        |
| Insertion at End             | **O(1)** *(if space available)* |
| Deletion                     | **O(n)**                        |
| Updating                     | **O(1)**                        |

---

# Interview Questions

## 1. What are the basic operations performed on an array?

**Answer:**
The basic array operations are:

* Accessing
* Traversal
* Searching
* Insertion
* Deletion
* Updating

---

## 2. What is traversal?

**Answer:**
Traversal is the process of visiting each element of an array exactly once to perform operations such as printing, searching, or calculating values.

---

## 3. Why is array access O(1)?

**Answer:**
Because the memory address of any element is calculated directly using its index, allowing immediate access without traversing the array.

---

## 4. What is the difference between accessing and searching?

**Answer:**

| Accessing                             | Searching                                          |
| ------------------------------------- | -------------------------------------------------- |
| Uses the index to retrieve an element | Finds the index of a given element                 |
| O(1)                                  | O(n) for Linear Search, O(log n) for Binary Search |
| Index is known                        | Index is unknown                                   |

---

## 5. Why is insertion in the middle of an array O(n)?

**Answer:**
Because all elements after the insertion point must be shifted one position to the right to make space for the new element.

---

## 6. Why is deletion O(n)?

**Answer:**
After deleting an element, all subsequent elements must be shifted one position to the left to maintain contiguous storage.

---

## 7. Why is updating O(1)?

**Answer:**
Updating uses the element's index to directly replace its value, requiring no traversal or shifting.

---

## 8. Which searching algorithm works on unsorted arrays?

**Answer:**
**Linear Search** works on both sorted and unsorted arrays because it checks each element sequentially.

---

## 9. Why does Binary Search require a sorted array?

**Answer:**
Binary Search eliminates half of the search space in each step. This is only possible if the elements are arranged in sorted order.

---

## 10. Which array operation is the most efficient?

**Answer:**
**Accessing** and **Updating** are the most efficient operations because both take **O(1)** time due to direct index-based access.
