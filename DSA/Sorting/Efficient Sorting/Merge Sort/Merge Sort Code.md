# Merge Sort in Java

## Merge Sort

```java
public class MergeSort {

    public static void mergeSort(int[] arr, int left, int right) {

        if (left < right) {

            int mid = left + (right - left) / 2;

            // Sort left half
            mergeSort(arr, left, mid);

            // Sort right half
            mergeSort(arr, mid + 1, right);

            // Merge both halves
            merge(arr, left, mid, right);
        }
    }

    public static void merge(int[] arr, int left, int mid, int right) {

        int n1 = mid - left + 1;
        int n2 = right - mid;

        int[] leftArray = new int[n1];
        int[] rightArray = new int[n2];

        // Copy data to temporary arrays
        for (int i = 0; i < n1; i++) {
            leftArray[i] = arr[left + i];
        }

        for (int j = 0; j < n2; j++) {
            rightArray[j] = arr[mid + 1 + j];
        }

        int i = 0;
        int j = 0;
        int k = left;

        // Merge the temporary arrays
        while (i < n1 && j < n2) {

            if (leftArray[i] <= rightArray[j]) {
                arr[k] = leftArray[i];
                i++;
            } else {
                arr[k] = rightArray[j];
                j++;
            }

            k++;
        }

        // Copy remaining elements of leftArray
        while (i < n1) {
            arr[k] = leftArray[i];
            i++;
            k++;
        }

        // Copy remaining elements of rightArray
        while (j < n2) {
            arr[k] = rightArray[j];
            j++;
            k++;
        }
    }

    public static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {

        int[] arr = {38, 27, 43, 3, 9, 82, 10};

        System.out.println("Before Sorting:");
        printArray(arr);

        mergeSort(arr, 0, arr.length - 1);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
38 27 43 3 9 82 10

After Sorting:
3 9 10 27 38 43 82
```

---

# Optimized Merge Sort

There is **no standard optimized version of Merge Sort** like Bubble Sort.

The implementation above is the standard recursive Merge Sort with **O(n log n)** time complexity.

Some practical optimizations used in libraries and production systems include:

* Using **Insertion Sort** for very small subarrays.
* Avoiding the merge step if the two halves are already in order.
* Using a single reusable temporary array instead of creating new arrays in every merge call.
* Implementing **Iterative (Bottom-Up) Merge Sort** to remove recursion overhead.

These optimizations improve practical performance but **do not change the asymptotic time complexity**, which remains **O(n log n)**.
