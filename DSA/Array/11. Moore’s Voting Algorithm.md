# Moore’s Voting Algorithm

## Definition

**Moore’s Voting Algorithm** is an efficient technique used to find the **majority element** in an array (an element that appears more than `n/2` times).

It works in **linear time O(n)** and **constant space O(1)** by using a voting mechanism instead of using extra data structures like hash maps.

---

# Problem Type

This algorithm is used when we need to:

* Find majority element (appears > n/2 times)
* Optimize frequency-based problems
* Reduce space complexity from O(n) to O(1)

---

# Key Idea

The main idea is:

* Pair up different elements and cancel them out.
* The element with majority frequency will survive at the end.

Think of it as a voting system:

* Same element → increases count
* Different element → decreases count
* When count becomes 0 → choose new candidate

---

# Algorithm Steps

1. Initialize:

   * `candidate = null`
   * `count = 0`

2. Traverse the array:

   * If `count == 0` → set current element as candidate
   * If current element == candidate → `count++`
   * Else → `count--`

3. After traversal, the candidate is the potential majority element.

4. (Optional but important) Verify if candidate is actually majority.

---

# Example

Array:

```text id="a1"
[2, 2, 1, 1, 1, 2, 2]
```

Steps:

```text id="a2"
Start: candidate = null, count = 0

2 → candidate = 2, count = 1
2 → count = 2
1 → count = 1
1 → count = 0
1 → candidate = 1, count = 1
2 → count = 0
2 → candidate = 2, count = 1
```

Final Candidate:

```text id="a3"
2
```

Verification:

* 2 appears 4 times → majority element

---

# Why It Works

The algorithm works on the principle of **pair cancellation**:

* Each occurrence of a non-majority element cancels one occurrence of the majority element.
* Since majority element appears more than n/2 times, it will always remain at the end.

---

# Applications

* Finding majority element in arrays
* Voting systems (conceptual model)
* Stream processing
* Frequency-based optimization problems
* Interview coding problems

---

# Advantages

* Runs in **O(n)** time
* Uses **O(1)** space
* Very efficient compared to hash map approach
* Simple and elegant logic

---

# Limitations

* Works only for elements occurring more than **n/2 times**
* Requires verification step to confirm correctness
* Cannot directly find all frequencies

---

# Complexity Analysis

## Time Complexity

```text id="t1"
O(n)
```

Only a single traversal of the array is required.

---

## Space Complexity

```text id="s1"
O(1)
```

Only two variables are used (candidate and count).

---

# Interview Questions

## 1. What is Moore’s Voting Algorithm?

**Answer:**
It is an algorithm used to find the majority element in an array in O(n) time and O(1) space using a voting mechanism.

---

## 2. What is a majority element?

**Answer:**
A majority element is an element that appears more than `n/2` times in an array of size `n`.

---

## 3. What is the main idea behind this algorithm?

**Answer:**
It uses a cancellation strategy where different elements cancel each other out, leaving the majority element.

---

## 4. Why is it called a voting algorithm?

**Answer:**
Because elements “vote” for candidates, and votes are increased or decreased based on matches.

---

## 5. What is the time complexity?

**Answer:**
The time complexity is **O(n)** because only one traversal is required.

---

## 6. What is the space complexity?

**Answer:**
The space complexity is **O(1)** because no extra data structures are used.

---

## 7. Do we always get a valid majority element?

**Answer:**
Not always. A verification step is needed to confirm whether the candidate actually appears more than n/2 times.

---

## 8. Can Moore’s Voting Algorithm find all frequent elements?

**Answer:**
No, it is only designed to find the element that appears more than half of the array size.

---

## 9. Why is this algorithm better than using a HashMap?

**Answer:**
Because it uses constant space (O(1)) instead of O(n) space required by a HashMap.

---

## 10. Where is this algorithm used?

**Answer:**
It is used in interview problems involving majority element detection and optimized frequency analysis.
