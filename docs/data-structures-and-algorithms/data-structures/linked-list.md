# 链表

## 单链表

```java
import java.util.Stack;

/**
 * 单链表
 *
 * @author 王越洋
 */
public class SingleLinkedListDemo {

  public static void main(String[] args) {
    // 先创建节点
    HeroNode hero1 = new HeroNode(1, "宋江", "及时雨");
    HeroNode hero2 = new HeroNode(2, "卢俊义", "玉麒麟");
    HeroNode hero3 = new HeroNode(3, "吴用", "智多星");
    HeroNode hero4 = new HeroNode(4, "入云龙", "公孙胜");
    HeroNode hero5 = new HeroNode(5, "大刀", "关胜");
    HeroNode hero6 = new HeroNode(6, "林冲", "豹子头");
    HeroNode hero7 = new HeroNode(7, "霹雳火", "秦明");
    HeroNode hero8 = new HeroNode(8, "双鞭", "呼延灼");

    // 创建一个链表
    SingleLinkedList singleLinkedList = new SingleLinkedList();
    SingleLinkedList singleLinkedList2 = new SingleLinkedList();
    // 加入
    // singleLinkedList.add(hero1);
    // singleLinkedList.add(hero2);
    // singleLinkedList.add(hero3);
    // singleLinkedList.add(hero4);

    singleLinkedList.addByOrder(hero1);
    singleLinkedList.addByOrder(hero4);
    singleLinkedList.addByOrder(hero7);
    singleLinkedList.addByOrder(hero3);

    singleLinkedList2.addByOrder(hero2);
    singleLinkedList2.addByOrder(hero5);
    singleLinkedList2.addByOrder(hero6);
    singleLinkedList2.addByOrder(hero8);

    HeroNode heroNode = mergeTwoLinkedList(singleLinkedList.getHead(), singleLinkedList2.getHead());
    assert heroNode != null;
    list(heroNode);

    // 修改节点信息
    // HeroNode newHero2 = new HeroNode(2, "小卢", "玉麒麟");
    // singleLinkedList.update(newHero2);

    // System.out.println("修改后的链表");
    // singleLinkedList.list();
    //
    // singleLinkedList.delete(1);
    // System.out.println("删除后的链表");
    // singleLinkedList.list();
    //
    // singleLinkedList.delete(3);
    // singleLinkedList.delete(1);
    // System.out.println("删除后的链表");
    // singleLinkedList.list();

    // 链表的有效节点个数
    // System.out.println("有效的节点个数为: " + getLength(singleLinkedList.getHead()));

    // int k = 1;
    // System.out.printf("倒数第 %d 个节点是: " + getLastIndexNode(singleLinkedList.getHead(), k) + "\n", k);

    // 反转链表
    // System.out.println("反转链表");
    // reverseLinkedList(singleLinkedList.getHead());
    // singleLinkedList.list();

    // 逆序打印
    // System.out.println("逆序打印");
    // reversePrint(singleLinkedList.getHead());
  }

  /**
   * 显示链表
   */
  public static void list(HeroNode head) {
    // 因为头节点不能动,需要一个辅助变量遍历
    HeroNode temp = head.next;
    while (true) {
      // 判断是否到链表的最后
      if (temp == null) {
        return;
      }
      // 输出节点信息
      System.out.println(temp);
      // 将temp后移
      temp = temp.next;
    }
  }

  /**
   * 返回单链表的有效长度,如果有头节点,不统计
   *
   * @param head 链表的头节点
   * @return 链表的有效节点的个数
   */
  public static int getLength(HeroNode head) {
    if (head.next == null) {
      return 0;
    }
    int length = 0;
    HeroNode currentNode = head;
    while (currentNode.next != null) {
      length++;
      currentNode = currentNode.next;
    }
    return length;
  }

  /**
   * 查找单链表的倒数第k个节点
   * <p>
   * 1. 编写一个方法,接收head节点,同时接收一个index
   * 2. index表示倒数第index个节点
   * 3. 先把链表从头到尾遍历,得到链表的总长度
   * 4. 得到size之后,从链表的第一个开始遍历 (size - index)个
   *
   * @param head 链表的头节点
   * @param k    倒数第k个节点
   */
  public static HeroNode getLastIndexNode(HeroNode head, int k) {
    // 判断如果链表为空,返回null
    if (head.next == null) {
      return null;
    }
    // 第一个遍历,得到链表的长度(节点个数)
    int size = getLength(head);
    // 第二次遍历, (size - index)位置就是倒数第k个
    if (k <= 0 || k > size) {
      // 越界
      return null;
    }
    HeroNode currentNode = head.next;
    for (int i = 0; i < (size - k); i++) {
      currentNode = currentNode.next;
    }
    return currentNode;
  }

  /**
   * 反转单链表
   *
   * @param head 原链表的头节点
   */
  public static void reverseLinkedList(HeroNode head) {

    if (head.next == null || head.next.next == null) {
      return;
    }

    // 先定义一个新的头节点
    HeroNode reverseHead = new HeroNode(0, "", "");

    // 定义一个临时节点代表当前节点
    HeroNode currentNode = head.next;
    // 指向当前节点currentNode的下一个节点
    HeroNode nextNode;

    while (currentNode != null) {
      // 保存当前节点的下一个节点
      nextNode = currentNode.next;
      // 将currentNode的下一个节点指向新的链表的最前端
      currentNode.next = reverseHead.next;
      // 将currentNode连接到新的链表上
      reverseHead.next = currentNode;
      // 将currentNode后移一位
      currentNode = nextNode;
    }
    head.next = reverseHead.next;
  }

  /**
   * 使用 栈 实现逆序打印
   * <p>
   * 栈: 先进后出
   */
  private static void reversePrint(HeroNode head) {
    if (head.next == null) {
      // 空链表
      return;
    }
    // 创建一个栈,将各个节点压入栈
    Stack<HeroNode> heroNodeStack = new Stack<>();
    HeroNode currentNode = head.next;

    while (currentNode != null) {
      heroNodeStack.push(currentNode);
      currentNode = currentNode.next;
    }

    // 遍历栈,将栈中的节点打印
    while (heroNodeStack.size() > 0) {
      System.out.println(heroNodeStack.pop());
    }
  }

  /**
   * 合并两个有序的单链表,合并之后的链表仍然有序
   * <p>
   * 去除了重复的数据
   */
  public static HeroNode mergeTwoLinkedList(HeroNode head1, HeroNode head2) {
    // 判断两个有序链表的长度,如有一为空,返回另一个,如果两个都为空,返回null
    if (head1.next == null && head2.next == null) {
      return null;
    }
    if (head1.next == null) {
      return head2;
    }
    if (head2.next == null) {
      return head1;
    }

    // 最终合并成的链表的当前操作节点
    HeroNode currentNode = head1.next;

    // 待加入的节点
    HeroNode temp = head2.next;

    // 待加入的节点的下一个节点
    HeroNode nextNode;

    while (temp != null) {
      // 要插入的节点比当前节点大
      if (temp.no > currentNode.no) {
        // 当前节点不是最后一个节点
        if (currentNode.next != null) {
          // 要插入的节点比当前节点的后面一个节点小,就应该在此插入
          if (temp.no < currentNode.next.no) {
            nextNode = temp.next;

            temp.next = currentNode.next;
            currentNode.next = temp;
            temp = nextNode;
            currentNode = currentNode.next;
          } else if (temp.no > currentNode.next.no) {
            // 要插入的节点比当前节点的下一个节点还大,当前节点要后移
            currentNode = currentNode.next;
          } else {
            temp = temp.next;
          }
        } else {
          // 要插入的节点放在最后一个位置
          currentNode.next = temp;
          temp = temp.next;
        }
      } else if (temp.no < currentNode.no) {
        // 要插入的节点比当前节点小(除了第一个,后面不可能出现这种情况)
        currentNode = currentNode.next;
      } else {
        temp = temp.next;
      }
    }
    return head1;
  }

}

/**
 * 定义SingleLinkedList,用于管理
 */
class SingleLinkedList {

  /**
   * 先初始化一个头节点,不要动. 不存放具体的数据
   */
  private HeroNode head = new HeroNode(0, "", "");

  /**
   * 返回头节点
   */
  public HeroNode getHead() {
    return head;
  }

  /**
   * 添加节点到单向链表,顺序添加,直接添加到尾部
   * <p>
   * 思路: 当不考虑编号顺序时,
   * 1.找到当前链表的最后节点
   * 2.将最后这个节点的next指向这个新节点
   */
  public void add(HeroNode heroNode) {
    // 因为head节点不能动,需要一个辅助遍历temp
    HeroNode temp = head;
    // 遍历链表,找到最后
    while (true) {
      // 找到链表的最后
      if (temp.next == null) {
        break;
      }
      // 没有找到最后,将temp后移
      temp = temp.next;
    }
    // 当while循环结束时,temp指向了链表的最后
    temp.next = heroNode;
  }

  /**
   * 第二种添加的方式,按照排名顺序添加
   */
  public void addByOrder(HeroNode heroNode) {
    HeroNode temp = head;

    // 添加的编号是否存在,默认为false
    boolean isExist = false;

    while (true) {
      if (temp.next == null) {
        // 说明temp已经在链表的最后
        break;
      }
      if (temp.next.no > heroNode.no) {
        // 位置找到,在temp的后面插入
        break;
      } else if (temp.next.no == heroNode.no) {
        // 希望添加的编号已经存在,不能再添加
        isExist = true;
        break;
      }
      // 后移,遍历当前链表
      temp = temp.next;
    }
    if (isExist) {
      // 不能添加,编号已存在
      System.out.printf("待插入的英雄的编号 %d 已存在, 不能加入\n", heroNode.no);
    } else {
      // 插入到temp后面
      heroNode.next = temp.next;
      temp.next = heroNode;
    }
  }

  /**
   * 修改节点的信息
   * 根据编号
   */
  public void update(HeroNode heroNode) {
    // 判断链表是否为空
    if (head.next == null) {
      System.out.println("链表为空");
      return;
    }
    /*
     找到要修改的节点
     */
    HeroNode temp = head.next;
    // 是否找到了该节点
    boolean isExist = false;

    while (true) {
      if (temp == null) {
        // 链表遍历结束
        break;
      }
      if (temp.no == heroNode.no) {
        // 该节点即为要修改的节点
        isExist = true;
        break;
      }
      temp = temp.next;
    }

    // 根据isFind,判断是否找到要修改的节点
    if (isExist) {
      temp.name = heroNode.name;
      temp.nickName = heroNode.nickName;
    } else {
      System.out.printf("没有找到编号为 %d 的节点", heroNode.no);
    }
  }

  /**
   * 删除节点信息
   * 根据编号
   */
  public void delete(int no) {

    HeroNode temp = head;
    boolean isExist = false;

    while (true) {
      if (temp.next == null) {
        System.out.println("链表为空或已经到了链表最后");
        break;
      }
      if (temp.next.no == no) {
        isExist = true;
        break;
      }
      temp = temp.next;
    }

    if (isExist) {
      temp.next = temp.next.next;
    } else {
      System.out.printf("要删除编号为 %d 的节点不存在\n", no);
    }
  }

  /**
   * 显示链表
   */
  public void list() {
    // 判断链表是否为空
    if (head.next == null) {
      System.out.println("链表为空");
      return;
    }
    // 因为头节点不能动,需要一个辅助变量遍历
    HeroNode temp = head.next;
    while (true) {
      // 判断是否到链表的最后
      if (temp == null) {
        return;
      }
      // 输出节点信息
      System.out.println(temp);
      // 将temp后移
      temp = temp.next;
    }
  }
}

/**
 * 定义HeroNode,每个对象就是一个节点
 * 梁山好汉
 */
class HeroNode {

  /**
   * 排名
   */
  public int no;

  /**
   * 姓名
   */
  public String name;

  /**
   * 称号
   */
  public String nickName;

  /**
   * 指向下一个节点
   */
  HeroNode next;

  public HeroNode(int no, String name, String nickName) {
    this.no = no;
    this.name = name;
    this.nickName = nickName;
  }

  @Override
  public String toString() {
    return "HeroNode{" +
        "no=" + no +
        ", name='" + name + '\'' +
        ", nickName='" + nickName + '\'' +
        '}';
  }
}
```



## 双向链表

```java
/**
 * 双向链表
 *
 * @author 王越洋
 */
public class DoubleLinkedListDemo {

  public static void main(String[] args) {
    System.out.println("双向链表的测试");

    // 先创建节点
    HeroNode2 hero1 = new HeroNode2(1, "宋江", "及时雨");
    HeroNode2 hero2 = new HeroNode2(2, "卢俊义", "玉麒麟");
    HeroNode2 hero3 = new HeroNode2(3, "吴用", "智多星");
    HeroNode2 hero4 = new HeroNode2(4, "入云龙", "公孙胜");

    HeroNode2 hero6 = new HeroNode2(6, "林冲", "豹子头");
    HeroNode2 hero7 = new HeroNode2(7, "霹雳火", "秦明");
    HeroNode2 hero8 = new HeroNode2(8, "双鞭", "呼延灼");

    // 创建一个双向链表
    DoubleLinkedList doubleLinkedList = new DoubleLinkedList();
    doubleLinkedList.add(hero1);
    doubleLinkedList.add(hero2);
    doubleLinkedList.add(hero3);
    doubleLinkedList.add(hero4);

    doubleLinkedList.list();

    // 修改
    HeroNode2 hero5 = new HeroNode2(3, "无用", "智多星");
    doubleLinkedList.update(hero5);
    System.out.println("修改后的链表");
    doubleLinkedList.list();

    // 删除
    doubleLinkedList.delete(3);
    System.out.println("删除后的链表");
    doubleLinkedList.list();
  }
}

/**
 * 创建一个双向链表的类
 */
class DoubleLinkedList {

  /**
   * 先初始化一个头节点,不要动. 不存放具体的数据
   */
  private HeroNode2 head = new HeroNode2(0, "", "");

  /**
   * 返回头节点
   */
  public HeroNode2 getHead() {
    return head;
  }

  /**
   * 遍历双向链表
   */
  public void list() {
    // 判断链表是否为空
    if (head.next == null) {
      System.out.println("链表为空");
      return;
    }
    // 因为头节点不能动,需要一个辅助变量遍历
    HeroNode2 temp = head.next;
    while (true) {
      // 判断是否到链表的最后
      if (temp == null) {
        return;
      }
      // 输出节点信息
      System.out.println(temp);
      // 将temp后移
      temp = temp.next;
    }
  }

  /**
   * 添加节点到双向链表,顺序添加,直接添加到尾部
   * <p>
   * 思路: 当不考虑编号顺序时,
   * 1.找到当前链表的最后节点
   * 2.将最后这个节点的next指向这个新节点
   */
  public void add(HeroNode2 heroNode) {
    // 因为head节点不能动,需要一个辅助遍历temp
    HeroNode2 temp = head;
    // 遍历链表,找到最后
    while (true) {
      // 找到链表的最后
      if (temp.next == null) {
        break;
      }
      // 没有找到最后,将temp后移
      temp = temp.next;
    }
    // 当while循环结束时,temp指向了链表的最后
    temp.next = heroNode;
    heroNode.pre = temp;
  }

  /**
   * 修改节点的信息
   * 根据编号
   * 和修改单向链表是一样的
   */
  public void update(HeroNode2 heroNode) {
    // 判断链表是否为空
    if (head.next == null) {
      System.out.println("链表为空");
      return;
    }
    /*
     找到要修改的节点
     */
    HeroNode2 temp = head.next;
    // 是否找到了该节点
    boolean isExist = false;

    while (true) {
      if (temp == null) {
        // 链表遍历结束
        break;
      }
      if (temp.no == heroNode.no) {
        // 该节点即为要修改的节点
        isExist = true;
        break;
      }
      temp = temp.next;
    }

    // 根据isFind,判断是否找到要修改的节点
    if (isExist) {
      temp.name = heroNode.name;
      temp.nickName = heroNode.nickName;
    } else {
      System.out.printf("没有找到编号为 %d 的节点", heroNode.no);
    }
  }

  /**
   * 从双向链表中删除一个节点
   */
  public void delete(int no) {
    // 判断当前链表是否为空
    if (head.next == null) {
      System.out.println("链表为空,无法删除");
      return;
    }
    HeroNode2 temp = head.next;
    boolean isExist = false;

    while (true) {
      if (temp == null) {
        // 没有找到
        break;
      }
      if (temp.no == no) {
        isExist = true;
        break;
      }
      temp = temp.next;
    }

    if (isExist) {
      temp.pre.next = temp.next;
      
      if (temp.next != null) {
        // 如果temp为最后一个节点,这句话会报错
        temp.next.pre = temp.pre;
      }
    } else {
      System.out.printf("要删除编号为 %d 的节点不存在\n", no);
    }
  }
}

/**
 * 定义HeroNode,每个对象就是一个节点
 * 梁山好汉
 */
class HeroNode2 {

  /**
   * 排名
   */
  public int no;

  /**
   * 姓名
   */
  public String name;

  /**
   * 称号
   */
  public String nickName;

  /**
   * 指向下一个节点
   */
  HeroNode2 next;

  /**
   * 指向上一个节点
   */
  HeroNode2 pre;

  public HeroNode2(int no, String name, String nickName) {
    this.no = no;
    this.name = name;
    this.nickName = nickName;
  }

  @Override
  public String toString() {
    return "HeroNode2{" +
        "no=" + no +
        ", name='" + name + '\'' +
        ", nickName='" + nickName + '\'' +
        '}';
  }
}
```



## 约瑟夫环(单向环形链表)<div id="Joseph"></div>

```java
/**
 * 约瑟夫问题-使用单向回环链表解决
 * <p>
 * 构建单向回环链表的思路
 * 1.先创建一个节点,让first指向该节点,并让其next指向自己形成回环
 * 2.后面每创建一个节点,就把该节点添加到回环中. 需借助辅助节点定位最后一个节点
 * <p>
 * 遍历回环链表的思路
 * 1.先让一个辅助节点currentNode指向first
 * 2.while循环遍历该环形链表,直到currentNode.next == first
 * <p>
 * 约瑟夫问题-小孩出圈问题
 * 1.创建一个辅助节点temp,指向环形链表的最后一个节点
 * 2.在出圈报数之前,先移动(k - 1)次
 * 3.当报数时,temp和first同时移动(m - 1)次
 * 4.此时first指向的节点出圈
 * first = first.next
 * temp.next = first
 *
 * @author 王越洋
 */
public class Joseph {

  public static void main(String[] args) {
    // 测试
    CircleSingleLinkedList circleSingleLinkedList = new CircleSingleLinkedList();
    circleSingleLinkedList.add(25);
    // circleSingleLinkedList.show();

    circleSingleLinkedList.count(5, 7, 25);
  }
}

/**
 * 创建环形单向链表
 */
class CircleSingleLinkedList {

  /**
   * 创建第一个节点
   */
  private Kid first = null;

  /**
   * 添加 num 个数量的节点
   *
   * @param num 要添加的数量
   */
  public void add(int num) {
    // 数量不能少于1
    if (num < 1) {
      System.out.println("数量太少,不能构成环形");
      return;
    }

    // 辅助节点指向最后的那一个节点
    Kid currentKid = null;

    for (int i = 1; i <= num; i++) {
      Kid kid = new Kid(i);
      if (i == 1) {
        first = kid;
        // 让第一个节点指向自己
        first.setNext(first);
        // 让辅助节点指向第一个
        currentKid = first;
      } else {
        // 辅助节点指向最新的那个节点
        currentKid.setNext(kid);
        // 最新的节点指向第一个
        kid.setNext(first);
        // 最新的节点赋值给辅助节点
        currentKid = kid;
      }
    }
  }

  /**
   * 展示单向链表
   */
  public void show() {
    if (first != null) {
      Kid currentKid = first;
      while (true) {
        System.out.printf("当前节点编号 %d \n", currentKid.getNo());
        if (currentKid.getNext() == first) {
          break;
        }
        currentKid = currentKid.getNext();
      }
    } else {
      System.out.println("链表为空");
    }
  }

  /**
   * 根据条件,计算出圈的顺序
   *
   * @param startNo  从第几个开始数数
   * @param countNum 每次数的次数
   * @param num      一共有多少个数
   */
  public void count(int startNo, int countNum, int num) {
    // 首先判断给出的条件是否合理
    if (startNo > num || countNum < 1 || first == null) {
      System.out.println("请重新输入参数");
      return;
    }
    // 创建辅助节点
    Kid temp = first;
    // 将temp指向最后一个节点
    while (temp.getNext() != first) {
      temp = temp.getNext();
    }

    // 将first和temp先同时移动(startNo - 1)次
    for (int i = 0; i < startNo - 1; i++) {
      first = first.getNext();
      temp = temp.getNext();
    }

    // 开始进行出圈行动,最后只剩下一个节点,停止行动
    while (first != temp) {
      for (int i = 0; i < countNum - 1; i++) {
        first = first.getNext();
        temp = temp.getNext();
      }
      // 此时,first即为要出圈的那个节点
      System.out.printf("要出圈的数是 %d \n", first.getNo());
      first = first.getNext();
      temp.setNext(first);
    }
    System.out.printf("最后一个数字是 %d \n", first.getNo());
  }
}

/**
 * 创建一个Kid类,表示一个节点
 */
class Kid {

  /**
   * 编号
   */
  private int no;

  /**
   * 指向下一个节点,默认为null
   */
  private Kid next;

  public Kid(int no) {
    this.no = no;
  }

  public int getNo() {
    return no;
  }

  public void setNo(int no) {
    this.no = no;
  }

  public Kid getNext() {
    return next;
  }

  public void setNext(Kid next) {
    this.next = next;
  }
}
```

