# Quick Sort in Java

## Quick Sort (Lomuto Partition Scheme)

```java
public class QuickSort {

    public static void quickSort(int[] arr, int low, int high) {

        if (low < high) {

            // Partition the array and get the pivot index
            int pivotIndex = partition(arr, low, high);

            // Sort left subarray
            quickSort(arr, low, pivotIndex - 1);

            // Sort right subarray
            quickSort(arr, pivotIndex + 1, high);
        }
    }

    public static int partition(int[] arr, int low, int high) {

        // Choose the last element as pivot
        int pivot = arr[high];

        int i = low - 1;

        for (int j = low; j < high; j++) {

            if (arr[j] < pivot) {

                i++;

                // Swap arr[i] and arr[j]
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }

        // Place pivot at its correct position
        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;

        return i + 1;
    }

    public static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {

        int[] arr = {10, 7, 8, 9, 1, 5};

        System.out.println("Before Sorting:");
        printArray(arr);

        quickSort(arr, 0, arr.length - 1);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
10 7 8 9 1 5

After Sorting:
1 5 7 8 9 10
```

---

# Optimized Quick Sort

The above implementation is the **basic Quick Sort** using the **Lomuto Partition Scheme**, where the **last element is chosen as the pivot**.

Several optimizations are commonly used in practice:

* **Random Pivot:** Select a random element as the pivot to reduce the chance of worst-case partitions.
* **Median of Three:** Choose the median of the first, middle, and last elements as the pivot for better partitioning.
* **Tail Recursion Elimination:** Recur on the smaller subarray first and iterate over the larger one to reduce recursion stack usage.
* **Insertion Sort for Small Subarrays:** Switch to Insertion Sort when subarrays become very small (typically 10–20 elements).

These optimizations improve practical performance but **do not change** the average-case time complexity, which remains **O(n log n)**.
