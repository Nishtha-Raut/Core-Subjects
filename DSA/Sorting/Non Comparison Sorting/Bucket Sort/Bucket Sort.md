# Bucket Sort

## Definition

Bucket Sort is a **distribution-based** sorting algorithm that divides the input elements into a number of **buckets**, sorts each bucket individually (using another sorting algorithm such as Insertion Sort), and then concatenates the sorted buckets to produce the final sorted array.

Bucket Sort is highly efficient when the input data is **uniformly distributed** over a known range. Its performance depends on how evenly the elements are distributed among the buckets.

---

# Algorithm / Approach

### Steps

1. Create a fixed number of empty buckets.
2. Distribute each element into its appropriate bucket based on its value.
3. Sort each bucket individually using a suitable sorting algorithm (commonly Insertion Sort).
4. Merge all the sorted buckets in order.
5. The merged array is the final sorted array.

---

# Complexity Analysis

| Case         | Time Complexity |
| ------------ | --------------- |
| Best Case    | **O(n + k)**    |
| Average Case | **O(n + k)**    |
| Worst Case   | **O(n²)**       |

Where:

* **n** = Number of elements
* **k** = Number of buckets

### Space Complexity

**O(n + k)**

* **O(n)** for storing elements in buckets.
* **O(k)** for maintaining the buckets.

---

# Properties

| Property          | Value                                               |
| ----------------- | --------------------------------------------------- |
| Sorting Technique | Distribution-Based                                  |
| Stable            | ✅ Yes *(If the bucket sorting algorithm is stable)* |
| In-place          | ❌ No                                                |
| Adaptive          | ❌ No                                                |
| Recursive         | ❌ No                                                |

---

# Interview Questions

## 1. What is Bucket Sort?

### Answer

Bucket Sort is a distribution-based sorting algorithm that distributes elements into multiple buckets, sorts each bucket individually, and then combines the sorted buckets to obtain the final sorted array.

---

## 2. Why is it called Bucket Sort?

### Answer

It is called Bucket Sort because the input elements are first divided into separate **buckets** based on their values before being sorted.

---

## 3. How does Bucket Sort work?

### Answer

Bucket Sort distributes elements into buckets according to their values. Each bucket is sorted separately, usually using Insertion Sort, and then all buckets are merged in order to produce the sorted array.

---

## 4. Why is Insertion Sort commonly used inside Bucket Sort?

### Answer

Insertion Sort performs very efficiently on small datasets. Since each bucket typically contains only a few elements, it is an ideal choice for sorting individual buckets.

---

## 5. What is the time complexity of Bucket Sort?

### Answer

* **Best Case:** `O(n + k)`
* **Average Case:** `O(n + k)`
* **Worst Case:** `O(n²)`

where:

* `n` = Number of elements
* `k` = Number of buckets

---

## 6. Why is the average-case complexity `O(n + k)`?

### Answer

When the input elements are uniformly distributed, each bucket contains only a few elements. Distributing elements into buckets and merging them takes linear time, making the overall complexity **O(n + k)**.

---

## 7. Why is the worst-case complexity `O(n²)`?

### Answer

If all elements fall into a single bucket, that bucket must be sorted independently. If Insertion Sort is used, the sorting time becomes **O(n²)**.

---

## 8. Is Bucket Sort stable?

### Answer

Yes, **if the sorting algorithm used for individual buckets is stable** (such as Insertion Sort). In that case, the relative order of equal elements is preserved.

---

## 9. Is Bucket Sort an in-place sorting algorithm?

### Answer

No.

Bucket Sort requires additional buckets to store elements before sorting, resulting in a space complexity of **O(n + k)**.

---

## 10. Is Bucket Sort adaptive?

### Answer

No.

Bucket Sort always distributes elements into buckets regardless of whether the input is already sorted.

---

## 11. What is the main advantage of Bucket Sort?

### Answer

Bucket Sort can achieve **near-linear performance** when the input data is uniformly distributed, making it very efficient for specific types of datasets.

---

## 12. What is the main disadvantage of Bucket Sort?

### Answer

Its performance heavily depends on the distribution of the input data. If the data is unevenly distributed, Bucket Sort can degrade to **O(n²)** time complexity.

---

## 13. What is the difference between Bucket Sort and Counting Sort?

### Answer

| Bucket Sort                                       | Counting Sort                                  |
| ------------------------------------------------- | ---------------------------------------------- |
| Distributes elements into buckets                 | Counts the frequency of each value             |
| Works well for uniformly distributed data         | Works well for a small range of integer values |
| Can sort floating-point numbers                   | Primarily sorts integers                       |
| Uses another sorting algorithm inside each bucket | Does not require another sorting algorithm     |

---

## 14. What is the difference between Bucket Sort and Radix Sort?

### Answer

| Bucket Sort                                    | Radix Sort                                       |
| ---------------------------------------------- | ------------------------------------------------ |
| Distributes elements into buckets              | Sorts numbers digit by digit                     |
| Performance depends on data distribution       | Performance depends on the number of digits      |
| Can sort floating-point numbers                | Mainly used for integers or fixed-length strings |
| Uses another sorting algorithm for each bucket | Uses Counting Sort internally                    |

---

## 15. Where is Bucket Sort used?

### Answer

Bucket Sort is commonly used for:

* Uniformly distributed data
* Floating-point numbers
* Probability distributions
* Large datasets with known value ranges
* Data preprocessing

---

## 16. When should Bucket Sort be used?

### Answer

Bucket Sort should be used when:

* The input data is **uniformly distributed**.
* The range of values is known.
* High performance is required for large datasets.
* Sorting floating-point numbers efficiently is needed.

It is generally **not recommended** for data with highly uneven distributions because its performance can degrade significantly.
