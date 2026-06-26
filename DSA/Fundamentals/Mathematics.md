# Algorithm Analysis

## Introduction

Before learning any Data Structure or Algorithm, it is important to understand **how to analyze an algorithm**. Two algorithms may solve the same problem but differ significantly in execution time and memory usage. Algorithm analysis helps us compare solutions and choose the most efficient one.

In this module, you will learn the mathematical concepts used to evaluate an algorithm's performance. These concepts are fundamental to coding interviews and are used throughout DSA.

---

# Topics Covered

## 1. Mathematics

Mathematics is the backbone of algorithm analysis. Basic mathematical concepts such as logarithms, exponents, summations, and growth of functions are used to analyze the efficiency of algorithms. Understanding these concepts makes it easier to derive and compare time complexities.

---

## 2. Time Complexity

Time Complexity measures how the execution time of an algorithm grows as the input size increases. Instead of measuring actual execution time, it focuses on the rate at which the algorithm's running time increases.

Time complexity helps us compare algorithms independently of hardware, compiler, or programming language.

---

## 3. Space Complexity

Space Complexity measures the amount of memory required by an algorithm during execution. It includes both the input space and the auxiliary space (extra memory used by the algorithm).

Efficient algorithms aim to optimize both time and memory usage.

---

## 4. Asymptotic Analysis

Asymptotic Analysis is the technique of evaluating an algorithm's efficiency for very large input sizes. It ignores constant factors and lower-order terms, allowing us to focus on the algorithm's growth rate.

It provides a machine-independent way to compare algorithms.

---

## 5. Big O Notation (O)

Big O Notation represents the **upper bound** or **worst-case** performance of an algorithm. It describes the maximum amount of time or space an algorithm may require as the input size grows.

Big O is the most commonly used notation in coding interviews.

---

## 6. Big Omega Notation (Ω)

Big Omega represents the **lower bound** or **best-case** performance of an algorithm. It indicates the minimum amount of time an algorithm will take under the most favorable conditions.

---

## 7. Big Theta Notation (Θ)

Big Theta represents the **tight bound** of an algorithm. It is used when both the upper bound and lower bound are the same, providing the exact asymptotic growth rate.

---

## 8. Best Case

Best Case refers to the scenario in which an algorithm performs the minimum possible number of operations. It represents the most favorable input for the algorithm.

---

## 9. Average Case

Average Case represents the expected performance of an algorithm over all possible inputs. It provides a realistic estimate of how an algorithm performs in practical situations.

---

## 10. Worst Case

Worst Case refers to the scenario in which an algorithm performs the maximum possible number of operations. Since it guarantees an upper performance limit, it is the most commonly analyzed case in interviews.

---

## 11. Amortized Analysis

Amortized Analysis evaluates the average cost of operations over a sequence of operations rather than considering a single operation in isolation. Although some operations may be expensive, the average cost per operation remains low over time.

A common example is the dynamic array, where resizing is costly but occurs infrequently, making the average insertion cost **O(1)**.

---

# Interview Questions

## 1. Why do we analyze algorithms?

**Answer:**
To compare different algorithms and choose the most efficient one in terms of execution time and memory usage.

---

## 2. What is the difference between Time Complexity and Space Complexity?

**Answer:**
Time Complexity measures the growth of execution time, whereas Space Complexity measures the growth of memory usage.

---

## 3. Why do we use Asymptotic Analysis?

**Answer:**
Because it provides a hardware-independent way to compare algorithms and focuses on their growth rate for large inputs.

---

## 4. What is the difference between Big O, Big Omega, and Big Theta?

**Answer:**

* **Big O (O):** Worst-case complexity
* **Big Omega (Ω):** Best-case complexity
* **Big Theta (Θ):** Exact (tight) bound

---

## 5. Which complexity is most commonly used in interviews?

**Answer:**
**Big O Notation**, because it describes the worst-case performance and provides an upper bound on an algorithm's running time.

---

## 6. What is the difference between Best Case, Average Case, and Worst Case?

**Answer:**

* **Best Case:** Minimum operations
* **Average Case:** Expected operations
* **Worst Case:** Maximum operations

---

## 7. What is Amortized Analysis?

**Answer:**
Amortized Analysis calculates the average cost of operations over a sequence of operations, even if some individual operations are expensive.

---

## 8. Why is Mathematics important in DSA?

**Answer:**
Mathematics helps analyze algorithms, solve recurrence relations, understand logarithms, and compare the growth rates of different algorithms, making it an essential foundation for DSA.
