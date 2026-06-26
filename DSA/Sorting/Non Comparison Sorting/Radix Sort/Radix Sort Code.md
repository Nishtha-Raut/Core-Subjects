# Radix Sort in Java

## Radix Sort (Using Counting Sort)

```java
public class RadixSort {

    // Function to perform Radix Sort
    public static void radixSort(int[] arr) {

        // Find the maximum element
        int max = getMax(arr);

        // Apply Counting Sort for each digit
        for (int exp = 1; max / exp > 0; exp *= 10) {
            countingSort(arr, exp);
        }
    }

    // Function to find the maximum element
    public static int getMax(int[] arr) {

        int max = arr[0];

        for (int num : arr) {
            if (num > max) {
                max = num;
            }
        }

        return max;
    }

    // Counting Sort based on the current digit
    public static void countingSort(int[] arr, int exp) {

        int n = arr.length;

        int[] output = new int[n];
        int[] count = new int[10];

        // Store the frequency of digits
        for (int i = 0; i < n; i++) {
            int digit = (arr[i] / exp) % 10;
            count[digit]++;
        }

        // Compute cumulative count
        for (int i = 1; i < 10; i++) {
            count[i] += count[i - 1];
        }

        // Build the output array (right to left for stability)
        for (int i = n - 1; i >= 0; i--) {

            int digit = (arr[i] / exp) % 10;

            output[count[digit] - 1] = arr[i];
            count[digit]--;
        }

        // Copy output array back to original array
        for (int i = 0; i < n; i++) {
            arr[i] = output[i];
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

        int[] arr = {170, 45, 75, 90, 802, 24, 2, 66};

        System.out.println("Before Sorting:");
        printArray(arr);

        radixSort(arr);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
170 45 75 90 802 24 2 66

After Sorting:
2 24 45 66 75 90 170 802
```

---

# Optimized Radix Sort

There is **no separate optimized version of Radix Sort** that changes its asymptotic complexity.

The implementation above is the **standard LSD (Least Significant Digit) Radix Sort**, which is the version most commonly asked in coding interviews.

Some practical optimizations include:

* Reusing the **output** and **count** arrays across digit passes to reduce memory allocations.
* Using a larger radix (such as **256**) instead of **10** for faster processing of binary data.
* Switching to **Insertion Sort** for very small arrays.
* Using **MSD Radix Sort** for variable-length strings.

These optimizations improve practical performance but **do not change** the overall time complexity, which remains **O(d × (n + k))**.
