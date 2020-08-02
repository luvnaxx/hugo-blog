# 两数之和


> 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
>
> 你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。
>
>  
>
> 示例:
>
> 给定 nums = [2, 7, 11, 15], target = 9
>
> 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/two-sum
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

<!--more-->

数组中同一元素不能使用两遍，使用双重循环，第一重循环循环第一个元素，第二重循环循环其后面的元素。
因为每种输入只会对应一个答案，找到即退出循环

```java
class Solution {

  public int[] twoSum(int[] nums, int target) {
    int[] res = new int[2];
    for (int i = 0; i < nums.length - 1; i++) {
      for (int j = i + 1; j < nums.length; j++) {
        if (nums[i] + nums[j] == target) {
          res[0] = i;
          res[1] = j;
          return res;
        }
      }
    }
    return res;
  }

//   public static void main(String[] args) {
//     Solution solution = new Solution();
//
//     int[] nums = {2, 7, 11, 15};
//     int target = 22;
//     int[] ints = solution.twoSum(nums, target);
//     System.out.println(Arrays.toString(ints));
//   }
}
```

![][1]



[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/07/5f441ab547c84b0086144b20849523df.png