# 寻找两个正序数组的中位数


> 给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。
>
> 请你找出这两个正序数组的中位数，并且要求算法的时间复杂度为 O(log(m + n))。
>
> 你可以假设 nums1 和 nums2 不会同时为空。
>
>  
>
> 示例 1:
>
> nums1 = [1, 3]
> nums2 = [2]
>
> 则中位数是 2.0
> 示例 2:
>
> nums1 = [1, 2]
> nums2 = [3, 4]
>
> 则中位数是 (2 + 3)/2 = 2.5
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/median-of-two-sorted-arrays
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

<!--more-->

#### 不符合时间复杂度的解题

> 先将两个数组合并成一个大数组，取中间的一个或者两个数

```java
class Solution {

  public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int A = 0, B = 0, C = 0;
    int[] nums = new int[nums1.length + nums2.length];
    while (A < nums1.length && B < nums2.length) {
      if (nums1[A] <= nums2[B]) {
        nums[C] = nums1[A];
        A++;
        C++;
      } else if (nums1[A] > nums2[B]) {
        nums[C] = nums2[B];
        B++;
        C++;
      }
    }
    if (A == nums1.length) {
      while (B < nums2.length) {
        nums[C++] = nums2[B++];
      }
    } else if (B == nums2.length) {
      while (A < nums1.length) {
        nums[C++] = nums1[A++];
      }
    }

    double i;
    if (nums.length % 2 == 0) {
      i = (nums[nums.length / 2 - 1] + nums[nums.length / 2]) / 2.0;
    } else {
      i = nums[nums.length / 2] * 1.0;
    }
    return i;
  }

  public static void main(String[] args) {
    int[] nums1 = {1, 3};
    int[] nums2 = {2};
    Solution solution = new Solution();
    double v = solution.findMedianSortedArrays(nums1, nums2);
    System.out.println(v);
  }
}
```

![][1]



[1]:  https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/08/a96ae1b3b750495a87ae0b2206d6046f.png