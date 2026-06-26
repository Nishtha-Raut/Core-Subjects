# Selection Sort in Java

## Basic Selection Sort

```java
public class SelectionSort {

    public static void selectionSort(int[] arr) {
        int n = arr.length;

        for (int i = 0; i < n - 1; i++) {

            int minIndex = i;

            // Find the index of the minimum element
            for (int j = i + 1; j < n; j++) {
                if (arr[j] < arr[minIndex]) {
                    minIndex = j;
                }
            }

            // Swap the minimum element with the current element
            int temp = arr[minIndex];
            arr[minIndex] = arr[i];
            arr[i] = temp;
        }
    }

    public static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }

    public static void main(String[] args) {

        int[] arr = {64, 25, 12, 22, 11};

        System.out.println("Before Sorting:");
        printArray(arr);

        selectionSort(arr);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
64 25 12 22 11

After Sorting:
11 12 22 25 64
```

---

# Optimized Selection Sort

There is **no optimized version of Selection Sort** that improves its time complexity.

Unlike Bubble Sort, Selection Sort must scan the remaining unsorted portion of the array in every pass to find the minimum element. Therefore, its best, average, and worst-case time complexities all remain **O(n²)**.

The implementation shown above is the standard and most efficient form of Selection Sort.
