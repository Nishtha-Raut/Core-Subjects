# Sorting - Complete Notes

---

# Table of Contents

1. Introduction
2. Why Sorting is Important
3. Sorting Terminology
4. Stability
5. In-Place Algorithms
6. Adaptive Algorithms
7. Internal vs External Sorting
8. Bubble Sort
9. Selection Sort
10. Insertion Sort
11. Merge Sort
12. Quick Sort
13. Heap Sort
14. Counting Sort
15. Radix Sort
16. Bucket Sort
17. Important Comparisons
18. Interview Questions & Answers
---

# 1. Introduction

Sorting is the process of arranging data in a specific order.

## Types

### Ascending Order

```text id="j19wxe"
1 2 3 4 5
```

### Descending Order

```text id="h3n9uv"
5 4 3 2 1
```

---

# Why Do We Need Sorting?

## Faster Searching

Binary Search only works on sorted data.

---

## Data Analysis

Easy to find:

* Maximum
* Minimum
* Median
* Duplicates

---

## Database Systems

Records are generally displayed in sorted order.

---

## Data Processing

Many algorithms assume sorted input.

---

# 2. Sorting Terminology

---

## Stable Sorting

Equal elements maintain their relative order.

Example:

Before:

```text id="gfm4hn"
5A 3 5B
```

After Stable Sort:

```text id="5im1zw"
3 5A 5B
```

A remains before B.

---

## Unstable Sorting

Relative order may change.

Example:

```text id="8p1bpx"
3 5B 5A
```

---

## In-Place Sorting

Uses constant extra memory.

Examples:

* Bubble Sort
* Selection Sort
* Insertion Sort
* Quick Sort
* Heap Sort

---

## Not In-Place

Requires extra memory.

Examples:

* Merge Sort
* Counting Sort
* Radix Sort

---

## Adaptive Sorting

Performs better when input is partially sorted.

Examples:

* Insertion Sort
* Optimized Bubble Sort

---

## Non-Adaptive Sorting

Performance remains same regardless of input.

Examples:

* Selection Sort
* Merge Sort
* Heap Sort

---

# 3. Bubble Sort

---

# Definition

Bubble Sort repeatedly compares adjacent elements and swaps them if they are in the wrong order.

Largest element moves to the end in each pass.

---

# Algorithm

1. Compare adjacent elements.
2. Swap if left > right.
3. Continue till end.
4. Repeat for remaining unsorted part.

---

# Dry Run

Array:

```text id="xnpfso"
5 1 4 2 8
```

---

## Pass 1

Compare 5 and 1

```text id="l2t9gz"
1 5 4 2 8
```

Compare 5 and 4

```text id="2km6a9"
1 4 5 2 8
```

Compare 5 and 2

```text id="xz7g9z"
1 4 2 5 8
```

Compare 5 and 8

No swap

```text id="m8a0k3"
1 4 2 5 8
```

Largest element fixed:

```text id="gyrhzd"
8
```

---

## Pass 2

Compare 1 and 4

No swap

Compare 4 and 2

```text id="od6r8v"
1 2 4 5 8
```

Compare 4 and 5

No swap

---

## Pass 3

Already sorted.

```text id="7c5t6t"
1 2 4 5 8
```

---

# Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n)       |
| Average | O(n²)      |
| Worst   | O(n²)      |

---

# Space Complexity

```text id="vq1qq5"
O(1)
```

---

# Stable

```text id="9d8z2y"
Yes
```

---

# In Place

```text id="1gk7c7"
Yes
```

---

# Advantages

* Easy implementation
* Good for teaching

---

# Disadvantages

* Very slow
* Not used in real systems

---

# Interview Question

## Why is Bubble Sort called Bubble Sort?

Because the largest element "bubbles up" to its correct position after each pass.

---

# 4. Selection Sort

---

# Definition

Repeatedly selects the minimum element and places it at its correct position.

---

# Algorithm

1. Find minimum.
2. Swap with first unsorted position.
3. Repeat.

---

# Dry Run

Array:

```text id="mlkczc"
64 25 12 22 11
```

---

## Pass 1

Minimum:

```text id="a5oshu"
11
```

Swap with 64

```text id="9d4dn9"
11 25 12 22 64
```

---

## Pass 2

Minimum:

```text id="yw7v1i"
12
```

Swap with 25

```text id="7c5n7z"
11 12 25 22 64
```

---

## Pass 3

Minimum:

```text id="oz4i6s"
22
```

Swap

```text id="xwdl7u"
11 12 22 25 64
```

---

Sorted.

---

# Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n²)      |
| Average | O(n²)      |
| Worst   | O(n²)      |

---

# Space Complexity

```text id="j8wzdx"
O(1)
```

---

# Stable

```text id="q92k4f"
No
```

---

# In Place

```text id="r9c2gg"
Yes
```

---

# Advantages

* Fewer swaps
* Easy implementation

---

# Disadvantages

* Slow
* Not adaptive

---

# Interview Question

## Why is Selection Sort not stable?

Because swapping may change the order of equal elements.

---

# 5. Insertion Sort

---

# Definition

Builds a sorted array one element at a time.

Works similar to arranging playing cards.

---

# Algorithm

1. Assume first element is sorted.
2. Pick next element.
3. Insert it into correct position.
4. Repeat.

---

# Dry Run

Array:

```text id="rpr1sh"
5 3 4 1 2
```

---

## Step 1

Insert 3

```text id="ajlprn"
3 5 4 1 2
```

---

## Step 2

Insert 4

```text id="t5wd9g"
3 4 5 1 2
```

---

## Step 3

Insert 1

```text id="h6o6h7"
1 3 4 5 2
```

---

## Step 4

Insert 2

```text id="hmh2bm"
1 2 3 4 5
```

---

# Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n)       |
| Average | O(n²)      |
| Worst   | O(n²)      |

---

# Space Complexity

```text id="k8qj7d"
O(1)
```

---

# Stable

```text id="r9v7k9"
Yes
```

---

# Adaptive

```text id="gf4vxz"
Yes
```

---

# In Place

```text id="2v24yf"
Yes
```

---

# Advantages

* Excellent for small datasets
* Fast for nearly sorted arrays
* Stable

---

# Disadvantages

* Slow for large datasets

---

# Interview Question

## Why is Insertion Sort adaptive?

Because if data is already nearly sorted, very few shifts are required.

Best-case complexity becomes:

```text id="b8q6s4"
O(n)
```

---

# Quick Comparison

| Property  | Bubble | Selection | Insertion |
| --------- | ------ | --------- | --------- |
| Best Case | O(n)   | O(n²)     | O(n)      |
| Average   | O(n²)  | O(n²)     | O(n²)     |
| Worst     | O(n²)  | O(n²)     | O(n²)     |
| Stable    | Yes    | No        | Yes       |
| In Place  | Yes    | Yes       | Yes       |
| Adaptive  | Yes    | No        | Yes       |

---

# Placement Interview Takeaway

If asked about basic sorting algorithms:

### Bubble Sort

Repeated adjacent swapping.

### Selection Sort

Repeated minimum selection.

### Insertion Sort

Repeated insertion into sorted portion.

### Best for Nearly Sorted Data

Insertion Sort

### Least Number of Swaps

Selection Sort

### Stable Basic Sorts

Bubble Sort and Insertion Sort

---




---

# 11. Merge Sort

---

# Definition

Merge Sort is a Divide and Conquer algorithm.

It repeatedly divides the array into smaller parts until each part contains one element and then merges them back in sorted order.

---

# Divide and Conquer

### Divide

Split array into two halves.

### Conquer

Recursively sort both halves.

### Combine

Merge sorted halves.

---

# Dry Run

Array:

```text
38 27 43 3 9
```

---

## Step 1: Divide

```text
38 27 43 | 3 9
```

---

## Step 2: Divide Again

```text
38 | 27 43

3 | 9
```

---

## Step 3: Divide Again

```text
38

27 | 43

3

9
```

---

## Step 4: Merge

Merge:

```text
27 43
```

Result:

```text
27 43
```

---

Merge:

```text
38 + (27 43)
```

Result:

```text
27 38 43
```

---

Merge:

```text
3 + 9
```

Result:

```text
3 9
```

---

Final Merge

```text
27 38 43
3 9
```

Result:

```text
3 9 27 38 43
```

---

# Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n log n) |
| Average | O(n log n) |
| Worst   | O(n log n) |

---

# Space Complexity

```text
O(n)
```

---

# Stable

```text
Yes
```

---

# In Place

```text
No
```

---

# Advantages

* Guaranteed O(n log n)
* Stable
* Predictable performance

---

# Disadvantages

* Extra memory required

---

# Interview Question

## Why is Merge Sort Stable?

While merging, equal elements are copied in their original order.

---

# Interview Question

## Why is Merge Sort O(n log n)?

There are:

```text
log n
```

levels of recursion.

At each level:

```text
n
```

elements are processed.

Therefore:

```text
O(n × log n)
```

---

# 12. Quick Sort

---

# Definition

Quick Sort is a Divide and Conquer algorithm.

It selects a pivot and partitions the array around it.

---

# Pivot

A pivot is the element used to divide the array.

Common choices:

* First element
* Last element
* Random element
* Median

---

# Dry Run

Array:

```text
10 7 8 9 1
```

Pivot:

```text
1
```

---

## Partition

Elements smaller than pivot:

```text
none
```

Pivot:

```text
1
```

Elements larger:

```text
10 7 8 9
```

Result:

```text
1 10 7 8 9
```

---

Sort right part:

```text
10 7 8 9
```

Pivot:

```text
9
```

Partition:

```text
7 8 | 9 | 10
```

Result:

```text
1 7 8 9 10
```

---

# Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n log n) |
| Average | O(n log n) |
| Worst   | O(n²)      |

---

# Space Complexity

```text
O(log n)
```

---

# Stable

```text
No
```

---

# In Place

```text
Yes
```

---

# Advantages

* Very fast in practice
* Excellent cache locality
* Low memory usage

---

# Disadvantages

Worst case:

```text
O(n²)
```

when pivot selection is poor.

---

# Interview Question

## Why is Quick Sort usually faster than Merge Sort?

Because:

* Better cache locality
* In-place sorting
* Smaller constant factors

Even though both are O(n log n) on average.

---

# Interview Question

## How can we avoid Quick Sort worst case?

Use:

* Random Pivot
* Median of Three
* Randomized Quick Sort

---

# 13. Heap Sort

---

# Definition

Heap Sort uses a Max Heap to sort elements.

---

# Idea

1. Build Max Heap.
2. Largest element reaches root.
3. Swap root with last element.
4. Heapify remaining elements.
5. Repeat.

---

# Dry Run

Array:

```text
4 10 3 5 1
```

---

## Build Max Heap

```text
10
/ \
5 3
/ \
4 1
```

Array:

```text
10 5 3 4 1
```

---

## Swap Root with Last

```text
1 5 3 4 10
```

Heapify:

```text
5 4 3 1 10
```

---

## Repeat

Swap:

```text
1 4 3 5 10
```

Heapify:

```text
4 1 3 5 10
```

Continue.

Final:

```text
1 3 4 5 10
```

---

# Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n log n) |
| Average | O(n log n) |
| Worst   | O(n log n) |

---

# Space Complexity

```text
O(1)
```

---

# Stable

```text
No
```

---

# In Place

```text
Yes
```

---

# Advantages

* Guaranteed O(n log n)
* No extra memory

---

# Disadvantages

* Slower than Quick Sort in practice

---

# Interview Question

## Why is Heap Sort not stable?

Heap operations can change the order of equal elements.

---

# 14. Counting Sort

---

# Definition

Counting Sort is a non-comparison sorting algorithm.

It counts the frequency of elements.

---

# Dry Run

Array:

```text
4 2 2 8 3
```

---

## Count Frequency

```text
2 -> 2

3 -> 1

4 -> 1

8 -> 1
```

---

## Build Sorted Array

```text
2 2 3 4 8
```

---

# Complexity

```text
O(n + k)
```

where:

```text
k = range of values
```

---

# Space Complexity

```text
O(k)
```

---

# Stable

```text
Yes
```

---

# In Place

```text
No
```

---

# Advantages

Very fast for small ranges.

---

# Disadvantages

Memory inefficient for huge ranges.

---

# Interview Question

## Why is Counting Sort not comparison based?

It never compares elements.

It only counts frequencies.

---

# 15. Radix Sort

---

# Definition

Radix Sort sorts numbers digit by digit.

Usually from least significant digit (LSD) to most significant digit (MSD).

---

# Dry Run

Array:

```text
170 45 75 90 802
```

---

## Ones Digit Sort

```text
170
90
802
45
75
```

---

## Tens Digit Sort

```text
802
45
170
75
90
```

---

## Hundreds Digit Sort

```text
45
75
90
170
802
```

Final Sorted Array.

---

# Complexity

```text
O(d × (n + k))
```

Where:

* d = number of digits
* k = digit range

---

# Stable

```text
Yes
```

---

# In Place

```text
No
```

---

# Advantages

Very fast for integers.

---

# Disadvantages

Works only for specific data types.

---

# 16. Bucket Sort

---

# Definition

Bucket Sort distributes elements into buckets.

Each bucket is sorted separately.

---

# Dry Run

Array:

```text
0.78 0.17 0.39 0.26 0.72
```

---

## Buckets

Bucket 0:

```text
0.17
```

Bucket 1:

```text
0.26
```

Bucket 2:

```text
0.39
```

Bucket 3:

empty

Bucket 4:

empty

Bucket 5:

empty

Bucket 6:

empty

Bucket 7:

```text
0.72
0.78
```

---

Merge buckets:

```text
0.17 0.26 0.39 0.72 0.78
```

---

# Complexity

Average:

```text
O(n)
```

Worst:

```text
O(n²)
```

---

# Best Use Case

Uniformly distributed data.

---

# 17. Important Comparison

| Algorithm | Stable | In Place | Adaptive |
| --------- | ------ | -------- | -------- |
| Bubble    | Yes    | Yes      | Yes      |
| Selection | No     | Yes      | No       |
| Insertion | Yes    | Yes      | Yes      |
| Merge     | Yes    | No       | No       |
| Quick     | No     | Yes      | No       |
| Heap      | No     | Yes      | No       |
| Counting  | Yes    | No       | No       |
| Radix     | Yes    | No       | No       |

---

# 18. Most Asked Interview Questions

## Which sorting algorithm is best?

There is no universally best sorting algorithm.

Depends on:

* Input size
* Memory constraints
* Stability requirements

---

## Which sorting algorithm is fastest in practice?

Quick Sort.

---

## Which sorting algorithm guarantees O(n log n)?

* Merge Sort
* Heap Sort

---

## Which sorting algorithm is best for nearly sorted data?

Insertion Sort.

---

## Which sorting algorithm uses Divide and Conquer?

* Merge Sort
* Quick Sort

---

## Which sorting algorithms are stable?

* Bubble Sort
* Insertion Sort
* Merge Sort
* Counting Sort
* Radix Sort

---

## Which sorting algorithms are in-place?

* Bubble Sort
* Selection Sort
* Insertion Sort
* Quick Sort
* Heap Sort

---

## Which sorting algorithms are non-comparison based?

* Counting Sort
* Radix Sort
* Bucket Sort

---

# Placement Interview Revision

### O(n²)

* Bubble
* Selection
* Insertion

### O(n log n)

* Merge
* Quick (Average)
* Heap

### Non-Comparison

* Counting
* Radix
* Bucket

### Stable + O(n log n)

* Merge Sort

### Fastest Practical Choice

* Quick Sort

### Guaranteed O(n log n)

* Merge Sort
* Heap Sort

### Best for Nearly Sorted Data

* Insertion Sort

# Sorting - Advanced Interview Notes 

---

# Table of Contents

1. Lower Bound of Comparison Sorting
2. Why Counting Sort is Faster
3. Stability Deep Dive
4. Quick Sort vs Merge Sort
5. Heap Sort vs Quick Sort
6. Cache Locality
7. Internal Working of Java Sorting
8. TimSort
9. External Sorting
10. Important Comparisons
11. Most Asked Interview Questions
12. 30-Second Interview Answer
13. Placement Revision Sheet

---

# 1. Lower Bound of Comparison Sorting

## Why Can't Comparison Sorting Beat O(n log n)?

### Answer

Any comparison-based sorting algorithm requires at least:

```text
O(n log n)
```

comparisons in the worst case.

Examples:

* Bubble Sort
* Selection Sort
* Insertion Sort
* Merge Sort
* Quick Sort
* Heap Sort

---

## Reason

Comparison sorting can be represented using a Decision Tree.

For n elements:

```text
Total possible permutations = n!
```

Every leaf of the tree represents one possible ordering.

Height of decision tree:

```text
log₂(n!)
```

Using Stirling's Approximation:

```text
log₂(n!) ≈ n log n
```

Therefore:

```text
Lower Bound = Ω(n log n)
```

---

# 2. Why Counting Sort is Faster

## Answer

Counting Sort does not compare elements.

Instead:

1. Count frequencies
2. Build cumulative counts
3. Construct sorted output

Complexity:

```text
O(n + k)
```

where:

```text
k = range of values
```

Because no comparisons are performed, it bypasses the comparison-sorting lower bound.

---

# 3. Stability Deep Dive

## Definition

A sorting algorithm is stable if equal elements preserve their relative order.

---

## Example

Before:

```text
(Alice, 50000)
(Bob, 30000)
(Charlie, 50000)
```

After Stable Sort (Salary):

```text
(Bob, 30000)
(Alice, 50000)
(Charlie, 50000)
```

Alice remains before Charlie.

---

## Why Stability Matters

Suppose records are first sorted by:

```text
Department
```

and then by:

```text
Salary
```

A stable sort preserves the previous department ordering among equal salaries.

---

## Stable Algorithms

* Bubble Sort
* Insertion Sort
* Merge Sort
* Counting Sort
* Radix Sort

---

## Unstable Algorithms

* Selection Sort
* Quick Sort
* Heap Sort

---

# 4. Quick Sort vs Merge Sort

## Quick Sort

### Advantages

* Faster in practice
* Better cache locality
* In-place sorting
* Less memory usage

### Disadvantages

```text
Worst Case = O(n²)
```

Not stable.

---

## Merge Sort

### Advantages

* Stable
* Guaranteed O(n log n)
* Predictable performance

### Disadvantages

Requires:

```text
O(n)
```

extra memory.

---

## Interview Answer

If stability is required, choose Merge Sort.

If memory is limited and average performance matters, choose Quick Sort.

---

# 5. Heap Sort vs Quick Sort

## Heap Sort

Advantages:

```text
Guaranteed O(n log n)
```

No extra memory.

Disadvantages:

```text
Poor cache locality
```

---

## Quick Sort

Advantages:

```text
Excellent practical performance
```

Disadvantages:

```text
Worst Case O(n²)
```

---

## Interview Answer

Quick Sort usually outperforms Heap Sort because of better cache performance and lower constant factors.

---

# 6. Cache Locality

## Definition

Cache locality refers to accessing memory locations that are physically close together.

---

## Array Example

```text
1000 → 10
1004 → 20
1008 → 30
1012 → 40
```

Memory is contiguous.

CPU loads nearby data efficiently.

---

## Linked List Example

```text
1000 → Node1
5000 → Node2
9000 → Node3
```

Memory is scattered.

More cache misses occur.

---

## Why Quick Sort Benefits

Partitioning scans contiguous portions of arrays.

Result:

```text
Fewer cache misses
```

which improves performance.

---

# 7. Internal Working of Java Sorting

## Arrays.sort() for Primitive Types

Examples:

```java
int[]
char[]
double[]
```

Uses:

```text
Dual Pivot Quick Sort
```

Average Complexity:

```text
O(n log n)
```

---

## Arrays.sort() for Objects

Examples:

```java
Student[]
Employee[]
String[]
```

Uses:

```text
TimSort
```

---

## Collections.sort()

Uses:

```text
TimSort
```

internally.

---

# 8. TimSort

## Definition

TimSort is a hybrid sorting algorithm.

Combination of:

```text
Merge Sort
+
Insertion Sort
```

---

## Used In

* Java
* Python

---

## Why TimSort?

Real-world data is often partially sorted.

TimSort detects already sorted runs and exploits them.

---

## Complexity

| Case    | Complexity |
| ------- | ---------- |
| Best    | O(n)       |
| Average | O(n log n) |
| Worst   | O(n log n) |

---

# 9. External Sorting

## Definition

Used when data cannot fit into RAM.

---

## Example

```text
500 GB File
8 GB RAM
```

Normal sorting cannot be performed entirely in memory.

---

## Solution

External Merge Sort

### Steps

1. Divide file into chunks.
2. Sort each chunk.
3. Store sorted chunks on disk.
4. Merge chunks.

---

## Applications

* Databases
* Search Engines
* Big Data Systems
* Data Warehouses

---

# 10. Important Comparisons

## Bubble vs Selection

| Feature   | Bubble | Selection |
| --------- | ------ | --------- |
| Stable    | Yes    | No        |
| Swaps     | More   | Less      |
| Best Case | O(n)   | O(n²)     |

---

## Selection vs Insertion

| Feature            | Selection | Insertion |
| ------------------ | --------- | --------- |
| Stable             | No        | Yes       |
| Adaptive           | No        | Yes       |
| Nearly Sorted Data | Poor      | Excellent |

---

## Merge vs Quick

| Feature         | Merge      | Quick     |
| --------------- | ---------- | --------- |
| Stable          | Yes        | No        |
| In Place        | No         | Yes       |
| Worst Case      | O(n log n) | O(n²)     |
| Practical Speed | Good       | Excellent |

---

## Quick vs Heap

| Feature         | Quick      | Heap       |
| --------------- | ---------- | ---------- |
| Average         | O(n log n) | O(n log n) |
| Worst           | O(n²)      | O(n log n) |
| Cache Locality  | Excellent  | Poor       |
| Practical Speed | Faster     | Slower     |

---

# 11. Most Asked Interview Questions

## Which Sorting Algorithm is Best?

There is no universally best sorting algorithm.

Choice depends on:

* Data size
* Memory availability
* Stability requirement
* Data distribution

---

## Which Sorting Algorithm is Fastest in Practice?

Quick Sort.

---

## Which Sorting Algorithms Guarantee O(n log n)?

* Merge Sort
* Heap Sort

---

## Which Sorting Algorithm is Best for Nearly Sorted Data?

Insertion Sort.

---

## Which Sorting Algorithms Use Divide and Conquer?

* Merge Sort
* Quick Sort

---

## Which Sorting Algorithms are Stable?

* Bubble Sort
* Insertion Sort
* Merge Sort
* Counting Sort
* Radix Sort

---

## Which Sorting Algorithms are In-Place?

* Bubble Sort
* Selection Sort
* Insertion Sort
* Quick Sort
* Heap Sort

---

## Which Sorting Algorithms are Non-Comparison Based?

* Counting Sort
* Radix Sort
* Bucket Sort

---

## Which Algorithm Does Java Use for Sorting?

Primitive arrays:

```text
Dual Pivot Quick Sort
```

Objects:

```text
TimSort
```

---

# 12. 30-Second Interview Answer

## Tell Me About Sorting

Sorting is the process of arranging data in a specific order. It improves searching, data processing, and analysis. Important properties include stability, in-place behavior, and adaptiveness. Basic sorting algorithms are Bubble Sort, Selection Sort, and Insertion Sort, while efficient algorithms include Merge Sort, Quick Sort, and Heap Sort. Non-comparison sorting algorithms such as Counting Sort and Radix Sort can achieve better than O(n log n) complexity under specific conditions.

---

# 13. Placement Revision Sheet

## Stable

* Bubble Sort
* Insertion Sort
* Merge Sort
* Counting Sort
* Radix Sort

---

## In-Place

* Bubble Sort
* Selection Sort
* Insertion Sort
* Quick Sort
* Heap Sort

---

## Adaptive

* Optimized Bubble Sort
* Insertion Sort

---

## Divide and Conquer

* Merge Sort
* Quick Sort

---

## Non-Comparison

* Counting Sort
* Radix Sort
* Bucket Sort

---

## Guaranteed O(n log n)

* Merge Sort
* Heap Sort

---

## Fastest Practical Choice

* Quick Sort

---

## Java Uses

* Primitive Arrays → Dual Pivot Quick Sort
* Objects → TimSort

---

# Final Placement Formula

Remember:

```text
Stable + O(n log n) = Merge Sort

Fastest Practical = Quick Sort

Guaranteed O(n log n) + In Place = Heap Sort

Best for Nearly Sorted Data = Insertion Sort

Small Integer Range = Counting Sort

Real-World Java Objects = TimSort
```
