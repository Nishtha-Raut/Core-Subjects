# 09 — Concurrency Coding Interview Problems (FooBar, ZeroEvenOdd, H2O)

These are classic **LeetCode "Concurrency" section** problems. They're a favorite in interviews because they test whether you can *apply* mutexes/condition variables/semaphores to real ordering constraints between threads — not just recite definitions.

---

## Problem 1: Print FooBar Alternately

### Problem Statement
- Same instance/object `foobar` is shared between **two threads**, `T1` and `T2`.
- `T1` calls `foo()`, `T2` calls `bar()`.
- We need the combined output to always be: **"foobar" printed `n` times in strict alternating order** — i.e., `foobarfoobarfoobar...` — `foo` must always print before `bar` in each pair.

### Why is this tricky?
- With a **single process/thread**, this is trivial: `FooBar FooBar FooBar...` happens naturally, sequentially.
- With **two threads** running concurrently with no coordination, you could get `foo, foo, bar, bar` or `bar, foo, foo, bar` — wrong. We need to **force strict alternation**.

```
        obj (shared instance of FooBar)
         /                        \
      T1 -> foo()             T2 -> bar()
         |                        |
         v                        v
       "Foo"                   "Bar"
```

### Solution — Using a lock + condition variable

**Key idea:** Use a shared boolean `turn` variable:
- Initially `turn = 0` (or `false`) → `T1`'s (foo's) turn.
- After `foo()` prints, set `turn = 1` and notify all → now it's `T2`'s (bar's) turn.
- After `bar()` prints, set `turn = 0` and notify all → back to `T1`'s turn.

```cpp
class FooBar {
private:
    int n;
    std::mutex m;
    std::condition_variable cv;
    bool turn;   // initially 0/false -> foo's turn

public:
    FooBar(int n) {
        this->n = n;
        turn = 0;
    }

    void foo(function<void()> printFoo) {
        for (int i = 0; i < n; i++) {
            std::unique_lock<std::mutex> lock(m);
            while (turn == 1) {
                cv.wait(lock);
            }
            printFoo();
            turn = 1;
            cv.notify_all();
        }
    }

    void bar(function<void()> printBar) {
        for (int i = 0; i < n; i++) {
            std::unique_lock<std::mutex> lock(m);
            while (turn == 0) {
                cv.wait(lock);
            }
            printBar();
            turn = 0;
            cv.notify_all();
        }
    }
};
```

### Trace (for n = 1)
```
Initially: turn = 0
T1 (foo) runs first: turn == 0 (its turn) -> print "Foo" -> turn = 1 -> notify
T2 (bar) was waiting: wakes up, sees turn == 1 (now its turn) -> print "Bar" -> turn = 0 -> notify
```

**Why `while` and not `if` before `cv.wait()`?**
> This is a classic interview follow-up. We use `while` (not `if`) to guard against **spurious wakeups** — a thread can wake up from `cv.wait()` even without an explicit `notify`, due to OS-level implementation details. Using `while` re-checks the condition after waking, ensuring correctness even if the wakeup wasn't "real."

**Real-life analogy:** Think of a **relay race with a baton** — Runner 1 (T1/foo) can only run when they're holding the baton (turn = 0). Once they finish their leg, they hand off the baton to Runner 2 (turn = 1) and shout "go!" (notify). Runner 2 was standing at the starting line waiting (condition variable wait) — not sprinting in place burning energy (not busy-waiting) — until they hear "go!"

---

## Problem 2: Print Zero Even Odd

### Problem Statement
- Given `n`, we have **3 threads**: `T_zero`, `T_odd`, `T_even`.
- `T_zero` should output `0`'s.
- `T_odd` should output **odd** numbers.
- `T_even` should output **even** numbers.
- Together, they must print the sequence: `0102030405...` up to `n` — i.e., a `0` always comes before each number, and odd/even numbers must appear in the correct order relative to their position.

**Example (n = 2):** Output should be `0` `1` `0` `2` — i.e., `T_zero` prints, then `T_odd` prints `1`, then `T_zero` prints again, then `T_even` prints `2`.

```
       0 1 0 2
      /  ^   \
  T_zero |  T_even
      T_odd
```

### Solution — Using `turn` variable (3-way coordination)

**Key idea:** A shared `turn` variable cycles through 3 states:
- `turn = 0` → `T_zero`'s turn (always prints 0 first).
- `turn = 1` → `T_odd`'s turn (prints the next odd number).
- `turn = 2` → `T_even`'s turn (prints the next even number).

```cpp
class ZeroEvenOdd {
private:
    int n;
    std::mutex m;
    std::condition_variable cv;
    int turn;   // 0 = zero's turn, 1 = odd's turn, 2 = even's turn
    int i;      // tracks the current number to print

public:
    ZeroEvenOdd(int n) {
        this->n = n;
        turn = 0;
        i = 1;
    }

    // printNumber(x) outputs "x", where x is an integer.
    void zero(function<void(int)> printNumber) {
        while (i <= n) {
            std::unique_lock<std::mutex> lock(m);
            while (turn != 0 && i <= n) {
                cv.wait(lock);
            }
            if (i > n) { cv.notify_all(); return; }
            printNumber(0);
            turn = (i % 2 == 0) ? 2 : 1;   // decide who goes next
            cv.notify_all();
        }
    }

    void odd(function<void(int)> printNumber) {
        while (i <= n) {
            std::unique_lock<std::mutex> lock(m);
            while (turn != 1 && i <= n) {
                cv.wait(lock);
            }
            if (i > n) { cv.notify_all(); return; }
            printNumber(i++);
            turn = 0;
            cv.notify_all();
        }
    }

    void even(function<void(int)> printNumber) {
        while (i <= n) {
            std::unique_lock<std::mutex> lock(m);
            while (turn != 2 && i <= n) {
                cv.wait(lock);
            }
            if (i > n) { cv.notify_all(); return; }
            printNumber(i++);
            turn = 0;
            cv.notify_all();
        }
    }
};
```

### Trace (for n = 2)
```
i=1, turn=0: T_zero prints "0". Since i(1) is odd -> turn = 1 (odd's turn next)
i=1, turn=1: T_odd prints "1", i becomes 2 -> turn = 0 (zero's turn next)
i=2, turn=0: T_zero prints "0". Since i(2) is even -> turn = 2 (even's turn next)
i=2, turn=2: T_even prints "2", i becomes 3 -> turn = 0
i=3 > n(2): loop ends for all threads
Output: 0 1 0 2  ✓
```

**Real-life analogy:** Think of a **relay hand-off between 3 runners** where a "zero runner" must touch the baton **before every single leg**, then hands it to either the odd-number runner or the even-number runner depending on whose turn is next — like a strict rotation MC at an event who always introduces a filler act ("0") before alternating between two main performers ("odd" and "even").

---

## Problem 3: Building H2O

### Problem Statement
- There are exactly **2 kinds of threads**: `hydrogen` and `oxygen`.
- Input is a string of `'H'` and `'O'` characters (e.g., `"HOH"`, `"OOHHHH"`).
- For each **water molecule**, you must output **exactly 2 `H`s and 1 `O`**, and threads must group correctly into valid molecules — meaning at any point, you can't release more than 2 `H` or more than 1 `O` "into the same molecule" without the matching count.
- Input guarantee: the total input will always have a **valid ratio** to form complete water molecules (2 H's for every 1 O).

**Key constraint:** the *within-molecule* order of H/H/O doesn't matter, but exactly 2 H threads + 1 O thread must be released together as a group before the next group starts.

### Why is this tricky?
- Unlike FooBar or ZeroEvenOdd (strict alternation of exactly 2 states), here we need **2 threads of one type + 1 thread of another type to synchronize as a "batch"** before the next batch proceeds.

### Solution — Using Semaphores

This uses a **custom Semaphore class** (built on mutex + condition variable) plus 2 semaphores to gate H and O threads into batches of the correct ratio.

```cpp
class Semaphore {
public:
    Semaphore() {}
    Semaphore(int c) : count(c) {}

    void setCount(int a) { count = a; }

    inline void signal() {
        std::unique_lock<std::mutex> lock(mtx);
        count++;
        if (count <= 0)
            cv.notify_one();
    }

    inline void wait() {
        std::unique_lock<std::mutex> lock(mtx);
        count--;
        while (count < 0) {
            cv.wait(lock);
        }
    }

private:
    std::mutex mtx;
    std::condition_variable cv;
    int count;
};

class H2O {
    Semaphore hydrogenSem, oxygenSem;
    std::mutex m;
    int hCount = 0;

public:
    H2O() {
        hydrogenSem.setCount(2);  // allow 2 hydrogen threads through per molecule
        oxygenSem.setCount(0);    // oxygen starts blocked until 2 H's have gone
    }

    void hydrogen(function<void()> releaseHydrogen) {
        hydrogenSem.wait();       // limits to 2 H's at a time in the "batch"
        {
            std::lock_guard<std::mutex> lock(m);
            releaseHydrogen();
            hCount++;
            if (hCount == 2) {
                oxygenSem.signal();   // 2 H's done -> let 1 O through
                hCount = 0;
            }
        }
        hydrogenSem.signal();     // allow next H into the semaphore's slot pool
    }

    void oxygen(function<void()> releaseOxygen) {
        oxygenSem.wait();         // blocked until 2 H's have signaled
        releaseOxygen();
        hydrogenSem.signal();     // release capacity? (implementation may vary)
        hydrogenSem.signal();     // opens 2 slots for the next batch of H's
    }
};
```

> **Note:** There are several valid implementations of H2O (using 2 counting semaphores for H and O, or a barrier-style approach). The core idea to remember for interviews is:
> - Use a **counting semaphore initialized to 2** to ensure **at most 2 Hydrogen threads** proceed per molecule.
> - Use a **counting semaphore initialized to 0** for Oxygen, which only gets signaled **after 2 Hydrogens have completed**, ensuring Oxygen waits for its 2 partners.
> - A `mutex`/lock_guard protects the shared counter (`hCount`) tracking how many H's have gone through in the current molecule.

**Real-life analogy:** Think of an **amusement park roller coaster car** that requires **exactly 2 kids (H) + 1 adult (O)** per car before it departs. Kids can queue up in pairs (semaphore limiting to 2 at a time getting "in position"), but the car **won't leave** (oxygen thread blocked) until exactly 2 kids have boarded. Once 2 kids are seated, the ride operator signals the adult to get in, and the car departs as a complete unit (molecule) — then the next batch of 2 kids + 1 adult can start queuing.

---

## Interview Q&A — Concurrency Coding Problems

**Q1: Why do we use `while` loops around `cv.wait()` instead of `if` in all three problems?**
> To guard against **spurious wakeups** (a thread waking from `wait()` without an actual `notify` call, which is allowed by the C++ standard and POSIX threads implementations) — re-checking the condition in a loop after waking ensures the thread only proceeds when the condition is genuinely true, not just because it happened to wake up.

**Q2: In FooBar, what would happen if you used `if(turn == 1) cv.wait(lock);` instead of `while`?**
> On a spurious wakeup, the thread could proceed to print even though it's not actually its turn yet, potentially violating the strict alternation and printing `foo foo` or `bar bar` back to back — a subtle race bug that's hard to catch in testing but can occur in production under certain thread-library implementations.

**Q3: What's the core design pattern common to all 3 problems?**
> All 3 use a **shared "turn"/counter variable protected by a mutex, combined with a condition variable (or semaphore) to block/wake threads** — instead of busy-waiting, threads sleep until signaled that it's their turn. The variation is in *what* the turn variable represents: a boolean (FooBar), a 3-state rotation (ZeroEvenOdd), or a batching counter (H2O).

**Q4: How would you extend ZeroEvenOdd to, say, 4 categories of threads (zero, odd, even, and "prime")?**
> Generalize the `turn` variable into a small state machine (e.g., an enum with 4 states) with clear transition rules for which state comes next based on the current number. The core primitives (mutex + condition variable, `while` loop re-check) stay exactly the same — only the state-transition logic changes. This is a good way to show an interviewer you understand the *pattern*, not just the specific memorized solution.

**Q5: Could you solve FooBar using a semaphore instead of a condition variable? How?**
> Yes — use **two binary semaphores**: `fooSem` initialized to 1, `barSem` initialized to 0.
> - `foo()`: `fooSem.wait()` → print → `barSem.signal()`.
> - `bar()`: `barSem.wait()` → print → `fooSem.signal()`.
> This achieves the same strict alternation without an explicit `turn` variable — each semaphore acts as a "permission token" passed back and forth. This is actually a cleaner, more idiomatic solution and a great one to mention in an interview to show you know multiple synchronization primitives.

---

**Next up (File 10):** Memory Management — Logical vs Physical Address Space, Contiguous Memory Allocation (Fixed & Dynamic Partitioning), Free Space Management (First/Next/Best/Worst Fit).
