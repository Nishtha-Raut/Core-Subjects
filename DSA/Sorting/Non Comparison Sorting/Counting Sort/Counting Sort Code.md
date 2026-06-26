# Counting Sort in Java

## Counting Sort

```java
public class CountingSort {

    public static void countingSort(int[] arr) {

        int n = arr.length;

        // Find the maximum element
        int max = arr[0];
        for (int num : arr) {
            if (num > max) {
                max = num;
            }
        }

        // Create count array
        int[] count = new int[max + 1];

        // Store the frequency of each element
        for (int num : arr) {
            count[num]++;
        }

        // Compute cumulative count
        for (int i = 1; i <= max; i++) {
            count[i] += count[i - 1];
        }

        // Create output array
        int[] output = new int[n];

        // Build the output array (Traverse from right to left to maintain stability)
        for (int i = n - 1; i >= 0; i--) {
            output[count[arr[i]] - 1] = arr[i];
            count[arr[i]]--;
        }

        // Copy output array back to original array
        for (int i = 0; i < n; i++) {
            arr[i] = output[i];
        }
    }

    public static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {

        int[] arr = {4, 2, 2, 8, 3, 3, 1};

        System.out.println("Before Sorting:");
        printArray(arr);

        countingSort(arr);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
4 2 2 8 3 3 1

After Sorting:
1 2 2 3 3 4 8
```

---

# Optimized Counting Sort

There is **no standard optimized version of Counting Sort** that changes its asymptotic complexity.

The implementation above is the standard and interview-preferred version because it is **stable** and runs in **O(n + k)** time.

Some practical optimizations include:

* **Reuse the count array** when sorting multiple arrays with the same value range.
* **Support negative numbers** by offsetting values using the minimum element.
* **Skip the cumulative count step** if stability is not required (only for specific use cases).

These optimizations improve memory usage or extend functionality but **do not change** the overall time complexity, which remains **O(n + k)**.
