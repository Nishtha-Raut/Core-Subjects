# Bucket Sort in Java

## Bucket Sort

```java
import java.util.ArrayList;
import java.util.Collections;

public class BucketSort {

    public static void bucketSort(float[] arr) {

        int n = arr.length;

        // Create n empty buckets
        ArrayList<Float>[] buckets = new ArrayList[n];

        for (int i = 0; i < n; i++) {
            buckets[i] = new ArrayList<>();
        }

        // Put array elements into different buckets
        for (int i = 0; i < n; i++) {
            int bucketIndex = (int) (arr[i] * n);
            buckets[bucketIndex].add(arr[i]);
        }

        // Sort individual buckets
        for (int i = 0; i < n; i++) {
            Collections.sort(buckets[i]);
        }

        // Concatenate all buckets into the original array
        int index = 0;

        for (int i = 0; i < n; i++) {

            for (float value : buckets[i]) {
                arr[index++] = value;
            }
        }
    }

    public static void printArray(float[] arr) {

        for (float num : arr) {
            System.out.print(num + " ");
        }

        System.out.println();
    }

    public static void main(String[] args) {

        float[] arr = {0.42f, 0.32f, 0.23f, 0.52f, 0.25f, 0.47f, 0.51f};

        System.out.println("Before Sorting:");
        printArray(arr);

        bucketSort(arr);

        System.out.println("After Sorting:");
        printArray(arr);
    }
}
```

### Output

```text
Before Sorting:
0.42 0.32 0.23 0.52 0.25 0.47 0.51

After Sorting:
0.23 0.25 0.32 0.42 0.47 0.51 0.52
```

---

# Optimized Bucket Sort

There is **no standard optimized version of Bucket Sort** that changes its asymptotic complexity.

The implementation above is the **standard Bucket Sort** used in interviews and academic courses.

Some practical optimizations include:

* Choosing the **optimal number of buckets** based on the input size and data distribution.
* Using **Insertion Sort** instead of `Collections.sort()` for buckets containing only a few elements.
* Dynamically resizing buckets to reduce memory overhead.
* Using **parallel processing** to sort multiple buckets simultaneously for very large datasets.

These optimizations improve practical performance but **do not change** the average-case time complexity, which remains **O(n + k)** for uniformly distributed data.
