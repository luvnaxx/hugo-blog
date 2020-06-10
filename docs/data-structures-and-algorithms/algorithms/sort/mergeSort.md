# 归并排序

```java
import java.util.Arrays;

public class Sort {

    public static void main(String[] args) {
        int[] nums = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};

        int[] temp = new int[nums.length];
        merger(nums, 0, nums.length - 1, temp);

        System.out.println(Arrays.toString(nums));
    }

    /**
     * 归并排序
     */
    private static void merger(int[] nums, int start, int end, int[] temp) {
        if (start < end) {
            int mid = (start + end) / 2;

            merger(nums, start, mid, temp);
            merger(nums, mid + 1, end, temp);

            mergerSort(nums, start, mid, end, temp);
        }
    }

    private static void mergerSort(int[] nums, int start, int mid, int end, int[] temp) {
        int i = start, j = mid + 1, t = 0;
        while (i <= start && j <= end) {
            if (nums[i] < nums[j]) {
                temp[t] = nums[i];
                t++;
                i++;
            } else {
                temp[t] = nums[j];
                t++;
                j++;
            }
        }
        while (i <= mid) {
            temp[t++] = nums[i++];
        }
        while (j <= mid) {
            temp[t++] = nums[j++];
        }

        for (int k = 0; k < t; k++) {
            nums[start] = temp[k];
            start++;
        }
    }
}
```