# 冒泡排序

```java
import java.util.Arrays;

public class Sort {

    public static void main(String[] args) {
        int[] nums = {8, 9, 1, 7, 2, 3, 5, 4, 6, 0};

        bubbleSort(nums);

        System.out.println(Arrays.toString(nums));
    }

    /**
     * 冒泡排序
     */
    private static void bubbleSort(int[] nums) {
        for (int i = 0; i < nums.length - 1; i++) {
            // 假设此次循环已经将数组完成排序
            boolean ordered = true;
            // 从头开始循环找出最大值放到最后面
            for (int j = 0; j < nums.length - 1 - i; j++) {
                // 此值比后一个相邻的值大
                if (nums[j] > nums[j + 1]) {
                    // 排序尚未完成
                    ordered = false;
                    // 将两值交换位置
                    int temp = nums[j];
                    nums[j] = nums[j + 1];
                    nums[j + 1] = temp;
                }
            }
            if (ordered) {
                // 已排序完成，提前结束循环
                break;
            }
        }
    }
}
```

