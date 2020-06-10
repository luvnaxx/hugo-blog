# 选择排序

```java
import java.util.Arrays;

public class Sort {

    public static void main(String[] args) {
        int[] nums = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};

        selectSort(nums);

        System.out.println(Arrays.toString(nums));
    }

    /**
     * 选择排序
     */
    private static void selectSort(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            // 假定待排序的数组的第一个值为最小值
            int minIndex = i;
            int min = nums[minIndex];
            // 遍历后面的全部数值
            for (int j = i + 1; j < nums.length; j++) {
                // 如果找到比最小值更小的值
                if (nums[j] < min) {
                    // 记录最小值的索引和值
                    minIndex = j;
                    min = nums[minIndex];
                }
            }
            // 将待排序的第一个值与最小值交换位置，最小值位于待排序数组的第一位
            nums[minIndex] = nums[i];
            nums[i] = min;
            // 下一遍循环，最小值被移出循环
        }
    }
    
}
```