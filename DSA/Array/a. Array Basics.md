# Array Basics

## Definition

An **Array** is a linear data structure that stores a collection of elements of the **same data type** in **contiguous memory locations**. Each element is identified by its **index**, which allows direct access in constant time.

Arrays are one of the most fundamental data structures and are widely used because of their simplicity and efficient random access.

---

# Characteristics of Arrays

An array has the following characteristics:

* Stores elements of the same data type.
* Elements are stored in contiguous memory locations.
* Each element is accessed using an index.
* The first element is stored at index **0** (in most programming languages like Java, C++, and Python).
* Supports fast random access.
* Static arrays have a fixed size after creation.

---

# Memory Representation

Array elements are stored one after another in memory.

Example:

```text
Array: [10, 20, 30, 40, 50]

Index:  0   1   2   3   4
```

If the base address of the array is `1000` and each integer occupies `4` bytes:

```text
Index      Address

0          1000
1          1004
2          1008
3          1012
4          1016
```

Address Formula:

```text
Address = Base Address + (Index × Size of Data Type)
```

This formula allows direct access to any element without traversing the array.

---

# Indexing

Indexing is the process of accessing elements using their position.

Example:

```java
int[] arr = {10, 20, 30, 40};

System.out.println(arr[0]); // 10
System.out.println(arr[2]); // 30
```

Since the memory location is calculated directly, accessing any element takes **O(1)** time.

---

# Advantages of Arrays

* Fast random access using index.
* Easy to traverse.
* Memory efficient due to contiguous storage.
* Simple to implement.
* Forms the foundation of many advanced data structures.
* Cache-friendly because elements are stored consecutively.

---

# Disadvantages of Arrays

* Static arrays have a fixed size.
* Insertion and deletion in the middle require shifting elements.
* Can only store elements of the same data type.
* Memory may be wasted if the allocated size is larger than required.
* Resizing a static array requires creating a new array and copying elements.

---

# Applications of Arrays

Arrays are used in many areas of computer science, including:

* Implementing stacks and queues.
* Representing matrices.
* Hash tables.
* Dynamic programming.
* Searching and sorting algorithms.
* Image processing.
* Graph representation (Adjacency Matrix).
* Heaps.
* Lookup tables.

---

# Complexity Analysis

| Operation                      | Time Complexity |
| ------------------------------ | --------------- |
| Access by Index                | **O(1)**        |
| Update                         | **O(1)**        |
| Traversal                      | **O(n)**        |
| Search (Linear)                | **O(n)**        |
| Search (Binary - Sorted Array) | **O(log n)**    |

---

# Interview Questions

## 1. What is an array?

**Answer:**
An array is a linear data structure that stores elements of the same data type in contiguous memory locations and allows direct access using an index.

---

## 2. Why are arrays called contiguous data structures?

**Answer:**
Because all elements are stored in consecutive memory locations, enabling direct access through address calculation.

---

## 3. Why does array indexing start from 0?

**Answer:**
The address of an element is calculated as:

```text
Address = Base Address + (Index × Size of Data Type)
```

When the index is `0`, the address is simply the base address. Starting from 0 simplifies address calculation and improves efficiency.

---

## 4. Why is accessing an array element O(1)?

**Answer:**
Because the memory address of any element is calculated directly using its index without traversing the array.

---

## 5. What is random access?

**Answer:**
Random access means any element can be accessed directly using its index in constant time, regardless of its position in the array.

---

## 6. What is the difference between an array and a linked list?

**Answer:**

| Array                     | Linked List             |
| ------------------------- | ----------------------- |
| Contiguous memory         | Non-contiguous memory   |
| O(1) random access        | O(n) access             |
| Fixed size (static array) | Dynamic size            |
| Fast access               | Fast insertion/deletion |

---

## 7. What are the advantages of arrays?

**Answer:**

* Constant-time access.
* Easy traversal.
* Cache-friendly.
* Simple implementation.
* Memory efficient.

---

## 8. What are the disadvantages of arrays?

**Answer:**

* Fixed size.
* Costly insertion and deletion.
* Homogeneous data only.
* Possible memory wastage.

---

## 9. Where are arrays commonly used?

**Answer:**
Arrays are used in sorting, searching, dynamic programming, matrices, heaps, hash tables, graph representations, and many other algorithms and data structures.

---

## 10. Why are arrays cache-friendly?

**Answer:**
Since array elements are stored in contiguous memory locations, the CPU cache can load nearby elements together, resulting in faster access and better performance compared to non-contiguous data structures.
