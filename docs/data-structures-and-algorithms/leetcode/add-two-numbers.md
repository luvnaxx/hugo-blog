# 两数相加


> 给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。
>
> 如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。
>
>  您可以假设除了数字 0 之外，这两个数都不会以 0 开头。
>
> 示例：
>
> 输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
>输出：7 -> 0 -> 8
> 原因：342 + 465 = 807
> 
>来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/add-two-numbers
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

<!--more-->

创建两个非空链表
由输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)，输出：7 -> 0 -> 8可知：数据从低位到高位，依次同位数相加，如结果大于10，将个位输出于此，高位进到上一位
将本节点的数相加得到结果。下个节点的数据循环此过程。
两个链表都没有了下一节点数据且本节点数据相加没有大于10，递归才算结束。

```java
public class ListNode {

  int val;
  ListNode next;

  ListNode(int x) {
    val = x;
  }
}

class Solution {

  public ListNode addTwoNumbers(ListNode l1, ListNode l2) {

    ListNode node = new ListNode(0);
    generateNode(l1, l2, node, 0);
    return node;
  }

  /**
   * 当前节点的两个数计算结果
   *
   * @param l1   A链表的当前节点
   * @param l2   B链表的当前节点
   * @param node 计算得到的输出节点
   * @param i    上个结果加给本节点的值
   */  
  private void generateNode(ListNode l1, ListNode l2, ListNode node, int i) {

    int res = l1.val + l2.val + i;
    int i1 = res % 10;
    int i2 = res / 10;

    node.val = i1;

    ListNode next = new ListNode(0);
    ListNode end = new ListNode(0);
    if (l1.next != null && l2.next != null) {
      node.next = next;
      generateNode(l1.next, l2.next, next, i2);
    } else if (l1.next == null && l2.next != null) {
      node.next = next;
      generateNode(end, l2.next, next, i2);
    } else if (l1.next != null) {
      node.next = next;
      generateNode(l1.next, end, next, i2);
    } else if (i2 != 0) {
      node.next = next;
      generateNode(end, end, next, i2);
    }
  }

  // public static void main(String[] args) {
  //   ListNode node11 = new ListNode(2);
  //   ListNode node12 = new ListNode(4);
  //   ListNode node13 = new ListNode(3);
  //   ListNode node14 = new ListNode(3);
  //   node11.next = node12;
  //   node12.next = node13;
  //   node13.next = node14;
  //
  //   ListNode node21 = new ListNode(5);
  //   ListNode node22 = new ListNode(6);
  //   ListNode node23 = new ListNode(4);
  //   node21.next = node22;
  //   node22.next = node23;
  //
  //   Solution solution = new Solution();
  //   ListNode node = solution.addTwoNumbers(node11, node21);
  //   System.out.println(node);
  // }
}
```

![][1]



[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/2020/07/a724239b35f44f08a1c08dbc769d8e5b.png