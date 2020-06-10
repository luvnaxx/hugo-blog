# 快速排序

```java
import java.util.Arrays;

public class Sort {

    public static void main(String[] args) {
        int[] nums = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};

        quickSort(nums, 0, nums.length - 1);
        
        System.out.println(Arrays.toString(nums));
    }

    /**
     * 快速排序
     *
     * @param nums  待排序数组
     * @param start 数组第一个值的下标
     * @param end   数组最后一个值的下标
     */
    private static void quickSort(int[] nums, int start, int end) {
        int i = start, j = end;
        int mid = nums[(i + j) / 2];
        while (i < j) {
            while (mid < nums[j]) {
                j--;
            }
            while (mid > nums[i]) {
                i++;
            }
            if (i >= j) {
                break;
            }
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
            if (nums[i] == mid) {
                j--;
            }
            if (nums[j] == mid) {
                i++;
            }
        }
        if (i - 1 > start) {
            quickSort(nums, start, i - 1);
        }
        if (j + 1 < end) {
            quickSort(nums, j + 1, end);
        }
    }

}
```

