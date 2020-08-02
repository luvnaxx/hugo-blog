# 无重复字符的最长子串


> 给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。
>
> 示例 1:
>
> 输入: "abcabcbb"
> 输出: 3 
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
> 示例 2:
>
> 输入: "bbbbb"
> 输出: 1
> 解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
> 示例 3:
>
> 输入: "pwwkew"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
>      请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/longest-substring-without-repeating-characters
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

<!--more-->

#### 第一版本

既然判断最长子串，那就从最长开始，判断每个子串中是否有重复字符，有的话查找下一个同长度子字符串。全都没有，找短一位的子字符串。
依次循环。

```java
class Solution {

  public int lengthOfLongestSubstring(String s) {
    int length = s.length();
    for (int i = length; i > 0; i--) {
      for (int j = 0; j + i <= length; j++) {
        if (!noRepeat(s.substring(j, j + i))) {
          return i;
        }
      }
    }
    return 0;
  }

  /**
   * 判断字符串是否包含重复字符
   *
   * @param str 待判断字符串
   * @return 是否包含重复字符，true:不重复;false:重复
   */
  private boolean noRepeat(String str) {
    char[] chars = str.toCharArray();
    Set<Character> characterSet = new HashSet<>();
    for (char aChar : chars) {
      characterSet.add(aChar);
    }
    return characterSet.size() == str.length();
  }

  // public static void main(String[] args) {
  //   Solution solution = new Solution();
  //   int length = solution.lengthOfLongestSubstring("abcabcbb");
  //   System.out.println(length);
  // }
}
```

> 在进行到最后一个测试用例时，因测试用例过长，导致超时。

![][1]

思想出了大问题，判断字符串中是否有重复字符，不必等待全部字符放入Set，在中间就可以判断
优化

```java
  private boolean noRepeat(String str) {
    char[] chars = str.toCharArray();
    Set<Character> characterSet = new HashSet<>();
    for (char aChar : chars) {
      if (characterSet.contains(aChar)) {
        return false;
      }
      characterSet.add(aChar);
    }
    return true;
  }
```



#### 第二版本

之前一直想的是从最大子字符串依次判断，有重复的就进行下一个同长度子字符串或者再小一位的子字符串。困在里面了

```java
class Solution {

  public int lengthOfLongestSubstring(String s) {

    if (s == null || s.length() < 1) {
      return 0;
    }
    char[] chars = s.toCharArray();
    int size = 0;

    for (int i = 0; i < chars.length; i++) {
      Set<Character> set = new HashSet<>();
      for (int j = i; j < chars.length; j++) {
        if (set.contains(chars[j])) {
          break;
        }
        set.add(chars[j]);
        size = Math.max(set.size(), size);
      }
    }
    return size;
  }

  public static void main(String[] args) {
    Solution solution = new Solution();
    int length = solution.lengthOfLongestSubstring("pwwkew");
    System.out.println(length);

  }
}
```

![][2]

还需优化



#### 第三版本

更好的方法是滑动窗口法。今天第一次听说接触。查找网上的例子，请看[滑动窗口法](../滑动窗口法.html)

代码实现

```java
class Solution {

  public int lengthOfLongestSubstring(String s) {

    char[] chars = s.toCharArray();
    int size = chars.length;
    int L = 0, R = -1;
    int maxLength = 0;
    Set<Character> set = new HashSet<>();

    while (R < size) {
      while (true) {
        R += 1;
        if (R == size) {
          break;
        }
        if (set.contains(chars[R])) {
          break;
        }
        maxLength = Math.max(maxLength, R - L + 1);
        set.add(chars[R]);
      }
      if (R == size) {
        break;
      }
      while (L < R) {
        if (chars[L] == chars[R]) {
          L += 1;
          break;
        }
        set.remove(chars[L]);
        L += 1;
      }
    }
    return maxLength;
  }

  public static void main(String[] args) {
    Solution solution = new Solution();
    int length = solution.lengthOfLongestSubstring("pwwkew");
    System.out.println(length);

  }
}
```

![][3]



[1]:  https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/07/cd2a14b7b59341588d351aad0771a435.png
[2]:  https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/07/68f6bdb618f4498984f0f8dfcfe4c8b7.png

[3]:  https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/07/10ee758c803f4a8f83bb738a298ae0f4.png