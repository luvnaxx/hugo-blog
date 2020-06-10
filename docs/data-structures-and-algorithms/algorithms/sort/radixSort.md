# 基数排序

```java
import java.util.Arrays;

public class RadixSort {

    public static void main(String[] args) {

        int[] nums = {53, 3, 542, 748, 14, 214};
        radixSort(nums);
        System.out.println(Arrays.toString(nums));
    }

    private static void radixSort(int[] nums) {

        int max = nums[0];
        for (int num : nums) {
            if (num > max) {
                max = num;
            }
        }
        int length = (max + "").length();

        int[][] bucket = new int[10][nums.length];
        int[] index;
        int temp;
        for (int i = 0; i < length; i++) {
            index = new int[10];
            temp = 0;
            for (int value : nums) {
                int times = (int) Math.pow(10, i);
                int num = (value / times % 10);
                bucket[num][index[num]] = value;
                index[num]++;
            }
            for (int j = 0; j < bucket.length; j++) {
                int k = index[j];
                if (k != 0) {
                    for (int n = 0; n < k; n++) {
                        nums[temp++] = bucket[j][n];
                    }
                }
            }
        }

        // // 第一次排序
        // int[][] bucket = new int[10][nums.length];
        // int[] index = new int[10];
        //
        // int temp = 0;
        // for (int j = 0; j < nums.length; j++) {
        //     // 每个数的个位数
        //     int num = (nums[j] % 10);
        //     bucket[num][index[num]] = nums[j];
        //     index[num]++;
        // }
        // for (int k = 0; k < bucket.length; k++) {
        //     int i1 = index[k];
        //     if (i1 != 0) {
        //         for (int i11 = 0; i11 < i1; i11++) {
        //             nums[temp++] = bucket[k][i11];
        //         }
        //     }
        // }
        // System.out.println(Arrays.toString(nums));
        //
        // // 第二次排序
        // temp = 0;
        // index = new int[10];
        // for (int i = 0; i < nums.length; i++) {
        //     // 每个数的个位数
        //     int num = (nums[i] / 10 % 10);
        //     bucket[num][index[num]] = nums[i];
        //     index[num]++;
        // }
        // for (int i = 0; i < bucket.length; i++) {
        //     int i1 = index[i];
        //     if (i1 != 0) {
        //         for (int i11 = 0; i11 < i1; i11++) {
        //             nums[temp++] = bucket[i][i11];
        //         }
        //     }
        // }
        // System.out.println(Arrays.toString(nums));
        //
        // // 第三次排序
        // temp = 0;
        // index = new int[10];
        // for (int i = 0; i < nums.length; i++) {
        //     // 每个数的个位数
        //     int num = (nums[i] / 100 % 10);
        //     bucket[num][index[num]] = nums[i];
        //     index[num]++;
        // }
        // for (int i = 0; i < bucket.length; i++) {
        //     int i1 = index[i];
        //     if (i1 != 0) {
        //         for (int i11 = 0; i11 < i1; i11++) {
        //             nums[temp++] = bucket[i][i11];
        //         }
        //     }
        // }
        // System.out.println(Arrays.toString(nums));
    }
}
```

