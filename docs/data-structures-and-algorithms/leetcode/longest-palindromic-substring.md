# 最长回文子串


> 给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。
>
> 示例 1：
>
>  输入: "babad"
>输出: "bab"
> 注意: "aba" 也是一个有效答案。
>示例 2：
> 
>输入: "cbbd"
> 输出: "bb"
> 
>来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/longest-palindromic-substring
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

<!--more-->

#### 方法一

> 暴力匹配
>
> 从大到小遍历全部子字符串，判断是否是回文子串

```java
class Solution {

  public String longestPalindrome(String s) {

    int length = s.length();
    while (length > 0) {
      for (int i = 0; i + length <= s.length(); i++) {
        String substring = s.substring(i, i + length);
        boolean b = isPalindrome(substring);
        if (b) {
          return substring;
        }
      }
      length--;
    }

    return s.substring(0, 0);
  }

  private boolean isPalindrome(String str) {
    int mid = str.length() / 2;
    if (str.length() % 2 == 0) {
      int temp1 = mid - 1;
      int temp2 = mid;
      while (mid > 0) {
        if (str.charAt(temp1--) != str.charAt(temp2++)) {
          return false;
        }
        mid--;
      }
    } else {
      int temp1 = mid;
      int temp2 = mid;
      while (mid >= 0) {
        if (str.charAt(temp1--) != str.charAt(temp2++)) {
          return false;
        }
        mid--;
      }
    }
    return true;
  }
}
```

![][1]



#### 方法二

> 动态规划

[1]: 