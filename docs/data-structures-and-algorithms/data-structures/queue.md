# 队列

## 普通队列

```java
import java.util.Scanner;

/**
 * 使用数组模拟队列
 * 问题: 不能复用,只能使用一次
 *
 * @author 王越洋
 */
public class ArrayQueueDemo {

  public static void main(String[] args) {
    // 创建一个队列
    ArrayQueue queue = new ArrayQueue(3);

    // 接收用户输入
    char key = ' ';
    Scanner scanner = new Scanner(System.in);

    boolean loop = true;

    // 输出一个菜单
    while (loop) {
      System.out.println("==== M E N U ====");
      System.out.println("s(show): 显示队列");
      System.out.println("e(exit): 退出程序");
      System.out.println("a(add): 添加数据到队列");
      System.out.println("g(get): 从队列取出数据");
      System.out.println("h(head): 查看队列头部的数据");

      //  接收一个字符
      key = scanner.next().charAt(0);
      switch (key) {
        case 's':
          queue.showQueue();
          break;
        case 'a':
          System.out.println("请输入一个数");
          int i = scanner.nextInt();
          queue.addQueue(i);
          break;
        case 'g':
          try {
            int res = queue.getQueue();
            System.out.printf("取出的数据是%d\n", res);
            break;
          } catch (Exception e) {
            System.out.println(e.getMessage());
          }
        case 'h':
          try {
            int res = queue.headQueue();
            System.out.printf("队列头部的数据是%d\n", res);
          } catch (Exception e) {
            System.out.println(e.getMessage());
          }
          break;
        case 'e':
          scanner.close();
          loop = false;
          break;
        default:
          System.out.println("输入的不符合预期字符");
      }
    }
    System.out.println("程序已退出");
  }
}

/**
 * ArrayQueue类
 */
class ArrayQueue {

  /**
   * 数组的最大容量
   */
  private int maxSize;

  /**
   * 指向队列的头部
   */
  private int front;

  /**
   * 指向队列的尾部
   */
  private int rear;

  /**
   * 该数组用于存放数据,模拟队列
   */
  private int[] arr;

  /**
   * 构造器
   * 创建队列
   */
  public ArrayQueue(int maxSize) {
    this.maxSize = maxSize;
    arr = new int[maxSize];
    // 指向队列头部的前一个位置
    front = -1;
    // 指向队列的尾部
    rear = -1;
  }

  /**
   * 判断队列是否已经满了
   */
  public boolean isFull() {
    return rear == maxSize - 1;
  }

  /**
   * 判断队列是否为空
   */
  public boolean isEmpty() {
    return front == rear;
  }

  /**
   * 添加数据
   *
   * @param item 新添加的数据
   */
  public void addQueue(int item) {
    if (isFull()) {
      System.out.println("Queue is full, can not fill in");
      return;
    }
    rear++;
    arr[rear] = item;
  }

  /**
   * 获取队列的数据,取出
   *
   * @return 当前取出的数据
   */
  public int getQueue() {
    if (isEmpty()) {
      // 通过抛出异常处理
      throw new RuntimeException("Queue is empty");
    }
    front++;
    return arr[front];
  }

  /**
   * 显示队列的全部数据
   */
  public void showQueue() {
    if (isEmpty()) {
      System.out.println("Queue is empty");
      return;
    }
    for (int i = 0; i < arr.length; i++) {
      System.out.printf("arr[%d] = %d\t", i, arr[i]);
    }
    System.out.println();
  }

  /**
   * 显示队列的头数据是什么,非取出
   */
  public int headQueue() {
    if (isEmpty()) {
      // 通过抛出异常处理
      throw new RuntimeException("Queue is empty");
    }
    return arr[front + 1];
  }

}
```



## 环形队列

```java
import java.util.Scanner;

/**
 * 数组模拟环形队列
 *
 * @author 王越洋
 */
public class CircleArrayQueueDemo {

  public static void main(String[] args) {
    System.out.println("测试数组模拟环形队列");
    // 创建一个队列,队列的有效数据为设置的maxSize - 1
    CircleArrayQueue queue = new CircleArrayQueue(4);

    // 接收用户输入
    char key = ' ';
    Scanner scanner = new Scanner(System.in);

    boolean loop = true;

    // 输出一个菜单
    while (loop) {
      System.out.println("==== M E N U ====");
      System.out.println("s(show): 显示队列");
      System.out.println("e(exit): 退出程序");
      System.out.println("a(add): 添加数据到队列");
      System.out.println("g(get): 从队列取出数据");
      System.out.println("h(head): 查看队列头部的数据");
      System.out.println("==== ↑ ↑ ↑ ↑ ====");

      //  接收一个字符
      key = scanner.next().charAt(0);
      switch (key) {
        case 's':
          queue.showQueue();
          break;
        case 'a':
          System.out.println("请输入一个数");
          int i = scanner.nextInt();
          queue.addQueue(i);
          break;
        case 'g':
          try {
            int res = queue.getQueue();
            System.out.printf("取出的数据是%d\n", res);
            break;
          } catch (Exception e) {
            System.out.println(e.getMessage());
          }
        case 'h':
          try {
            int res = queue.headQueue();
            System.out.printf("队列头部的数据是%d\n", res);
          } catch (Exception e) {
            System.out.println(e.getMessage());
          }
          break;
        case 'e':
          scanner.close();
          loop = false;
          break;
        default:
          System.out.println("输入的不符合预期字符");
      }
    }
    System.out.println("程序已退出");
  }
}


class CircleArrayQueue {

  /*
  当队列满时,(rear + 1) % maxSize == front
  当队列空时,rear == front
  队列中的有效数据,(rear + maxSize - front) % maxSize
 */

  /**
   * 数组的最大容量
   */
  private int maxSize;

  /**
   * front指向队列第一个元素,arr[front]就是队列第一个元素,初始值0
   */
  private int front;

  /**
   * rear指向队列最后一个元素的下一个位置,预留一个空间,初始值0
   */
  private int rear;

  /**
   * 该数组用于存放数据,模拟队列
   */
  private int[] arr;

  /**
   * 构造器
   * 创建队列
   */
  public CircleArrayQueue(int maxSize) {
    this.maxSize = maxSize;
    arr = new int[maxSize];
  }

  /**
   * 判断队列是否已经满了
   */
  public boolean isFull() {
    return (rear + 1) % maxSize == front;
  }

  /**
   * 判断队列是否为空
   */
  public boolean isEmpty() {
    return front == rear;
  }

  /**
   * 添加数据
   *
   * @param item 新添加的数据
   */
  public void addQueue(int item) {
    if (isFull()) {
      System.out.println("Queue is full, can not fill in");
      return;
    }
    arr[rear] = item;
    // 将rear后移,但是要考虑取模
    rear = (rear + 1) % maxSize;
  }

  /**
   * 获取队列的数据,取出
   *
   * @return 当前取出的数据
   */
  public int getQueue() {
    if (isEmpty()) {
      // 通过抛出异常处理
      throw new RuntimeException("Queue is empty");
    }
    // front指向队列的第一个元素
    // 1.将front对应的值保留到一个临时变量
    int item = arr[front];
    // 2.将front后移,考虑取模
    front = (front + 1) % maxSize;
    // 3.将临时变量返回
    return item;
  }

  /**
   * 显示队列的全部数据
   */
  public void showQueue() {
    if (isEmpty()) {
      System.out.println("Queue is empty");
      return;
    }
    // 从front开始遍历,遍历n个元素
    // 1.先计算n的数值
    int n = size();
    for (int i = front; i < front + n; i++) {
      System.out.printf("arr[%d] = %d\n", i % maxSize, arr[i % maxSize]);
    }
  }

  /**
   * 求出当前队列的有效数据个数
   */
  public int size() {
    return (rear + maxSize - front) % maxSize;
  }

  /**
   * 显示队列的头数据是什么,非取出
   */
  public int headQueue() {
    if (isEmpty()) {
      // 通过抛出异常处理
      throw new RuntimeException("Queue is empty");
    }
    return arr[front];
  }
}
```

