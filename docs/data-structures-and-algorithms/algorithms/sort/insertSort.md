# 插入排序

```java
import java.util.Arrays;

public class Sort {

    public static void main(String[] args) {
        int[] nums = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};

        insertSort(nums);

        System.out.println(Arrays.toString(nums));
    }


    /**
     * 插入排序
     */
    private static void insertSort(int[] nums) {

        // 将除第一个元素的数依次进行比较大小，进行插入
        for (int i = 1; i < nums.length; i++) {
            // 取出待排序数值
            int min = nums[i];
            // 在不越界的情况下，依次用前面已经排好序的数从后往前与此数值进行比较
            while (i > 0 && nums[i - 1] > min) {
                // 如果排好序的数比这个值大
                // 将值向后移动一位
                nums[i] = nums[i - 1];
                // 比较前面那个数值
                i--;
            }
            // 跳出while循环，说明比待插入数值大的数都已经后移完成，此时的i即为待插入数值的索引所在位置，赋值
            nums[i] = min;
        }

        // 将索引为1的数值进行插入排序
        // int i = 1;
        // int min = nums[i];
        // while (i > 0 && nums[i - 1] > min) {
        //     nums[i] = nums[i - 1];
        //     i--;
        // }
        // nums[i] = min;
        //
        // 将索引为2的数值进行插入排序
        // i = 2;
        // min = nums[i];
        // while (i > 0 && nums[i - 1] > min) {
        //     nums[i] = nums[i - 1];
        //     i--;
        // }
        // nums[i] = min;
        //
        // 将索引为3的数值进行插入排序
        // i = 3;
        // min = nums[i];
        // while (i > 0 && nums[i - 1] > min) {
        //     nums[i] = nums[i - 1];
        //     i--;
        // }
        // nums[i] = min;
    }
}
```