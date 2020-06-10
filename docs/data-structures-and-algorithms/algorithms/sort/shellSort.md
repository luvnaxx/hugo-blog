# 希尔排序

#### 交换法

```java
import java.util.Arrays;

public class Sort {

    public static void main(String[] args) {
        int[] nums = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};

        shellSort1(nums);

        System.out.println(Arrays.toString(nums));
    }

    /**
     * 希尔排序
     * 交换法
     */
    private static void shellSort1(int[] nums) {

        for (int gap = nums.length / 2; gap > 0; gap /= 2) {
            for (int i = gap; i < nums.length; i++) {
                for (int j = i - gap; j >= 0; j -= gap) {
                    if (nums[j] > nums[j + gap]) {
                        // 交换位置
                        int temp = nums[j];
                        nums[j] = nums[j + gap];
                        nums[j + gap] = temp;
                    }
                }
            }
        }

        // // 第一轮，分成 10/2 = 5 组，每组两个数值进行比较
        // for (int i = 5; i < nums.length; i++) {
        //     for (int j = i - 5; j >= 0; j -= 5) {
        //         if (nums[j] > nums[j + 5]) {
        //             // 交换位置
        //             int temp = nums[j];
        //             nums[j] = nums[j + 5];
        //             nums[j + 5] = temp;
        //         }
        //     }
        // }
        // // 3, 5, 1, 6, 0, 8, 9, 4, 7, 2
        //
        // // 第二轮，分成 10/2/2 = 2 组，每组五个数值进行比较
        // for (int i = 2; i < nums.length; i++) {
        //     for (int j = i - 2; j >= 0; j -= 2) {
        //         if (nums[j] > nums[j + 2]) {
        //             // 交换位置
        //             int temp = nums[j];
        //             nums[j] = nums[j + 2];
        //             nums[j + 2] = temp;
        //         }
        //     }
        // }
        // // 0, 2, 1, 4, 3, 5, 7, 6, 9, 8
        //
        // // 第三轮，分成 10/2/2/2 = 1 组，每组十个数值进行比较
        // for (int i = 1; i < nums.length; i++) {
        //     for (int j = i - 1; j >= 0; j -= 1) {
        //         if (nums[j] > nums[j + 1]) {
        //             // 交换位置
        //             int temp = nums[j];
        //             nums[j] = nums[j + 1];
        //             nums[j + 1] = temp;
        //         }
        //     }
        // }
        // // 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
    }
}
```

#### 位移法

```java
import java.util.Arrays;

public class Sort {

    public static void main(String[] args) {
        int[] nums = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};

        shellSort2(nums);

        System.out.println(Arrays.toString(nums));
    }
    /**
     * 希尔排序
     * 移位法
     */
    private static void shellSort2(int[] nums) {
        for (int gap = nums.length / 2; gap > 0; gap /= 2) {
            for (int i = gap; i < nums.length; i++) {
                int j = i;
                int min = nums[j];
                if (min < nums[j - gap]) {
                    while (j - gap >= 0 && min < nums[j - gap]) {
                        nums[j] = nums[j - gap];
                        j -= gap;
                    }
                    nums[j] = min;
                }
            }
        }

        // // 第一轮，分成 10/2 = 5 组，每组两个数值进行比较
        // for (int i = 5; i < nums.length; i++) {
        //     int j = i;
        //     int min = nums[j];
        //     if (min < nums[j - 5]) {
        //         while (j - 5 >= 0 && min < nums[j - 5]) {
        //             nums[j] = nums[j - 5];
        //             j -= 5;
        //         }
        //         nums[j] = min;
        //     }
        // }
        // // 3, 5, 1, 6, 0, 8, 9, 4, 7, 2
        //
        // // 第二轮，分成 10/2/2 = 2 组，每组五个数值进行比较
        // for (int i = 2; i < nums.length; i++) {
        //     int j = i;
        //     int min = nums[j];
        //     if (min < nums[j - 2]) {
        //         while (j - 2 >= 0 && min < nums[j - 2]) {
        //             nums[j] = nums[j - 2];
        //             j -= 2;
        //         }
        //         nums[j] = min;
        //     }
        // }
        // 0, 2, 1, 4, 3, 5, 7, 6, 9, 8
    }
}
```