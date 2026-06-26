# Recursion Fundamentals

## Introduction

Recursion is one of the most important concepts in Data Structures and Algorithms. It is widely used in problems involving trees, graphs, divide and conquer, dynamic programming, and backtracking. Understanding recursion helps in writing elegant solutions for complex problems by breaking them into smaller, similar subproblems.

This module introduces the fundamentals of recursion, different types of recursion, the function call stack, and important concepts like backtracking and memoization that build upon recursion.

---

# Topics Covered

## 1. Recursion Basics

Recursion is a programming technique in which a function calls itself to solve a smaller instance of the same problem. Every recursive function must have a **base case** to stop recursion and a **recursive case** that reduces the problem size.

Recursion simplifies many problems that have a repetitive or hierarchical structure.

---

## 2. Function Call Stack

Whenever a function is called, the system stores its execution details in the **call stack**. In recursion, each recursive call creates a new stack frame. Once the base case is reached, the stack starts unwinding, and each function returns its result in reverse order.

Understanding the call stack is essential for tracing recursive programs and avoiding stack overflow.

---

## 3. Tail Recursion

In Tail Recursion, the recursive call is the **last operation** performed by the function. No computation remains after the recursive call returns.

Tail-recursive functions are generally more memory efficient and can be optimized by some programming languages into iterative solutions.

---

## 4. Head Recursion

In Head Recursion, the recursive call is made **before** any processing is performed. The actual work happens while the recursion unwinds after reaching the base case.

Head recursion is commonly used when processing elements after recursive calls.

---

## 5. Tree Recursion

Tree Recursion occurs when a function makes **more than one recursive call** during a single execution. This creates a tree-like structure of recursive calls.

Examples include Fibonacci recursion, tree traversals, and divide-and-conquer algorithms.

---

## 6. Indirect Recursion

Indirect Recursion occurs when two or more functions call each other recursively instead of a function calling itself directly.

For example, Function A calls Function B, and Function B calls Function A until a base condition is satisfied.

---

## 7. Backtracking Basics

Backtracking is an algorithmic technique that builds a solution step by step. If the current path cannot lead to a valid solution, it **backtracks** by undoing the previous choice and tries another possibility.

It is commonly used in problems involving combinations, permutations, Sudoku, N-Queens, Rat in a Maze, and graph coloring.

---

## 8. Memoization Basics

Memoization is an optimization technique used with recursion. Instead of solving the same subproblem repeatedly, previously computed results are stored and reused.

Memoization significantly improves the performance of recursive algorithms and forms the basis of Dynamic Programming.

---

# Interview Questions

## 1. What is recursion?

**Answer:**
Recursion is a technique in which a function calls itself to solve smaller instances of the same problem until a base case is reached.

---

## 2. Why is a base case necessary in recursion?

**Answer:**
The base case stops further recursive calls. Without it, recursion continues indefinitely and eventually causes a **Stack Overflow Error**.

---

## 3. What is the function call stack?

**Answer:**
The function call stack is a memory structure that stores information about active function calls. Each recursive call creates a new stack frame, which is removed when the function returns.

---

## 4. What is Tail Recursion?

**Answer:**
Tail Recursion is a type of recursion where the recursive call is the last operation performed by the function.

---

## 5. What is Head Recursion?

**Answer:**
Head Recursion is a type of recursion where the recursive call occurs before any other processing in the function.

---

## 6. What is Tree Recursion?

**Answer:**
Tree Recursion occurs when a recursive function makes multiple recursive calls, producing a tree-like structure of function calls.

---

## 7. What is Indirect Recursion?

**Answer:**
Indirect Recursion occurs when two or more functions call each other recursively instead of directly calling themselves.

---

## 8. What is Backtracking?

**Answer:**
Backtracking is a problem-solving technique that explores all possible solutions by making choices and undoing them whenever a choice does not lead to a valid solution.

---

## 9. What is Memoization?

**Answer:**
Memoization is an optimization technique in which previously computed results are stored and reused to avoid redundant recursive computations.

---

## 10. What is the difference between Backtracking and Memoization?

**Answer:**

| Backtracking                    | Memoization                                |
| ------------------------------- | ------------------------------------------ |
| Explores all possible solutions | Stores previously computed results         |
| Undoes choices when needed      | Avoids repeated calculations               |
| Used for search problems        | Used for optimization problems             |
| Examples: Sudoku, N-Queens      | Examples: Fibonacci, Knapsack, DP problems |

---

## 11. What is the difference between Tail Recursion and Head Recursion?

**Answer:**

| Tail Recursion                            | Head Recursion                                |
| ----------------------------------------- | --------------------------------------------- |
| Recursive call is the last operation      | Recursive call occurs before processing       |
| Work is done before the recursive call    | Work is done after the recursive call returns |
| Can sometimes be optimized into iteration | Generally cannot be optimized in the same way |

---

## 12. Where is recursion commonly used?

**Answer:**
Recursion is commonly used in:

* Tree Traversals
* Graph Traversals (DFS)
* Binary Search
* Merge Sort
* Quick Sort
* Backtracking
* Dynamic Programming
* Divide and Conquer Algorithms
