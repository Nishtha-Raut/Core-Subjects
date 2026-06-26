# Insertion Sort in Java

## Insertion Sort

```java
public class InsertionSort {

    public static void insertionSort(int[] arr) {
        int n = arr.length;

        for (int i = 1; i < n; i++) {

            int key = arr[i];
            int j = i - 1;

            // Shift elements greater than key to one position ahead
            while (j >= 0 && arr[j] > key) {
                arr[j + 1] = arr[j];
                j--;
            }

            // Insert the key at its correct position
            arr[j + 1] = key;
        }
    }

    public static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {

        int[] arr = {12, 11, 13, 5, 6};

        System.out.println("Before Sorting:");
        printArray(arr);

        insertionSort(arr);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
12 11 13 5 6

After Sorting:
5 6 11 12 13
```

---

# Optimized Insertion Sort

There is **no separate optimized version of Insertion Sort** like Bubble Sort.

The standard implementation is already adaptive because:

* If the array is already sorted, the `while` loop never executes.
* Each element requires only one comparison.
* The algorithm automatically achieves a **best-case time complexity of O(n)** without any additional optimization.

Therefore, the implementation shown above is the standard and most efficient version of Insertion Sort.
