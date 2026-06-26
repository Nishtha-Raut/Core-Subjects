# Heap Sort in Java

## Heap Sort

```java
public class HeapSort {

    // Function to perform Heap Sort
    public static void heapSort(int[] arr) {
        int n = arr.length;

        // Build Max Heap
        for (int i = n / 2 - 1; i >= 0; i--) {
            heapify(arr, n, i);
        }

        // Extract elements from heap one by one
        for (int i = n - 1; i > 0; i--) {

            // Swap root (largest) with last element
            int temp = arr[0];
            arr[0] = arr[i];
            arr[i] = temp;

            // Heapify the reduced heap
            heapify(arr, i, 0);
        }
    }

    // Function to maintain Max Heap property
    public static void heapify(int[] arr, int n, int i) {

        int largest = i;
        int left = 2 * i + 1;
        int right = 2 * i + 2;

        // Check left child
        if (left < n && arr[left] > arr[largest]) {
            largest = left;
        }

        // Check right child
        if (right < n && arr[right] > arr[largest]) {
            largest = right;
        }

        // If largest is not the root
        if (largest != i) {

            int temp = arr[i];
            arr[i] = arr[largest];
            arr[largest] = temp;

            // Recursively heapify the affected subtree
            heapify(arr, n, largest);
        }
    }

    // Function to print the array
    public static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {

        int[] arr = {12, 11, 13, 5, 6, 7};

        System.out.println("Before Sorting:");
        printArray(arr);

        heapSort(arr);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
12 11 13 5 6 7

After Sorting:
5 6 7 11 12 13
```

---

# Optimized Heap Sort

There is **no separate optimized version of Heap Sort** that changes its asymptotic complexity.

The implementation above is the standard Heap Sort algorithm and is used in most educational and interview settings.

Some practical optimizations include:

* **Iterative Heapify:** Replace recursive `heapify()` with an iterative version to eliminate recursion overhead.
* **Bottom-Up Heap Construction:** Build the heap from the last non-leaf node, which already achieves **O(n)** heap construction.
* **Hybrid Sorting:** Switch to Insertion Sort for very small heaps to improve practical performance.

These optimizations improve runtime constants but **do not change** the overall time complexity, which remains **O(n log n)**.
