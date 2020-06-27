# 栈

## 使用数组模拟栈

```java
import java.util.Scanner;

/**
 * 使用数组模拟栈
 *
 * @author 王越洋
 */
public class ArrayStackDemo {

  public static void main(String[] args) {
    ArrayStack stack = new ArrayStack(5);
    Scanner scanner = new Scanner(System.in);
    String key;

    boolean loop = true;

    while (loop) {
      System.out.println("show:  显示栈");
      System.out.println("exit:  退出程序");
      System.out.println("push:  添加数据");
      System.out.println("pop:  取出数据");
      System.out.println("请输入");
      key = scanner.next();

      switch (key) {
        case "show":
          stack.list();
          break;
        case "push":
          System.out.println("请输入一个整数");
          int value = scanner.nextInt();
          stack.push(value);
          break;
        case "pop":
          try {
            int res = stack.pop();
            System.out.println("出栈的数据是: " + res);
            break;
          } catch (Exception e) {
            System.out.println(e.getMessage());
          }
        case "exit":
          scanner.close();
          loop = false;
          break;
        default:
          break;
      }
    }
  }
}

/**
 * 表示栈
 */
class ArrayStack {

  /**
   * 栈的大小
   */
  private int maxSize;

  /**
   * 数组模拟栈
   */
  private int[] stack;

  /**
   * top代表栈顶,初始化为-1
   */
  private int top = -1;

  public ArrayStack(int maxSize) {
    this.maxSize = maxSize;
    stack = new int[maxSize];
  }

  /**
   * 判断栈是否已经满了
   */
  public boolean isFull() {
    return top == maxSize - 1;
  }

  /**
   * 判断栈是否是空的
   */
  public boolean isEmpty() {
    return top != -1;
  }

  /**
   * 入栈
   */
  public void push(int value) {
    if (!isFull()) {
      top++;
      stack[top] = value;
    } else {
      System.out.println("栈满了");
    }
  }

  /**
   * 出栈
   */
  public int pop() {
    if (isEmpty()) {
      int value = stack[top];
      top--;
      return value;
    } else {
      throw new RuntimeException("栈是空的");
    }
  }

  /**
   * 遍历栈
   */
  public void list() {
    // 遍历要从栈顶开始向栈底
    if (isEmpty()) {
      for (int i = top; i >= 0; i--) {
        System.out.printf("stack[%d] = %d \n", i, stack[i]);
      }
    } else {
      System.out.println("栈是空的");
    }
  }
}
```



## 使用单链表模拟栈

```java
/**
 * 使用单链表模拟栈
 *
 * @author 王越洋
 */
public class LinkedStackDemo {

  public static void main(String[] args) {
    LinkedStack stack = new LinkedStack();
    stack.push(1);
    stack.push(2);
    stack.push(3);
    stack.push(4);
    stack.push(5);
    stack.push(6);
    int length = stack.length();
    System.out.println(length);
  }
}

/**
 * 对栈的一系列操作
 */
class LinkedStack {

  private Node top;

  public void push(int value) {
    Node node = new Node(value);
    // 第一个值
    if (top == null) {
      top = node;
    } else {
      // 将新的放在栈顶
      node.next = top;
      top = node;
    }
  }

  /**
   * 出栈
   */
  public int pop() {
    if (isEmpty()) {
      throw new RuntimeException("栈是空的!");
    }
    int data = top.data;
    top = top.next;
    return data;
  }


  /**
   * 返回栈顶元素
   */
  public int peek() {
    if (isEmpty()) {
      throw new RuntimeException("栈是空的!");
    }
    return top.data;
  }


  /**
   * 栈中元素总数
   */
  public int length() {
    if (top == null) {
      return 0;
    }
    Node temp = top;
    // 计数器
    int count = 0;
    while (temp != null) {
      temp = temp.next;
      count++;
    }

    return count;
  }


  /**
   * 判空
   */
  public boolean isEmpty() {
    return top == null;
  }
}

/**
 * 单链表
 * 模拟栈
 */
class Node {

  /**
   * 当前节点的值
   */
  int data;

  /**
   * 下一个节点
   */
  Node next;

  public Node(int value) {
    this.data = value;
  }
}
```



## 使用栈完成计算器功能

```java
/**
 * 使用栈完成计算器功能
 * <p>
 * 暂仅支持加减乘除
 *
 * 问题: 只能计算正整数
 *
 * @author 王越洋
 */
public class Calculator {

  public static void main(String[] args) {
    String expression = "10+3*3-9/3";

    // 用于遍历表达式的每一位,定位
    int index = 0;
    // 数栈中取出的第一个数
    int num1 = 0;
    // 数栈中取出的第二个数
    int num2 = 0;
    // 表达式中的操作运算符
    int operator = 0;
    // 计算的结果
    int res = 0;
    // 表达式中的当前被扫描到的值
    char c = ' ';

    // 拼接数字
    String keepNum = "";

    // 数栈
    ArrayStack2 numStack = new ArrayStack2(10);
    // 运算符栈
    ArrayStack2 operatorStack = new ArrayStack2(10);

    while (true) {
      c = expression.substring(index, index + 1).charAt(0);

      // 判断当前扫描到的字符是运算符还是数字
      if (operatorStack.isOperator(c)) {
        // 判断当前符号栈是否为空
        if (operatorStack.isEmpty()) {
          // 如果为空,直接将运算符放入
          operatorStack.push(c);
        } else { // 不为空,判断当前运算符与栈顶的运算符的权重
          if (operatorStack.priority(c) > operatorStack.priority(operatorStack.peek())) {
            // 当前操作的运算符的优先级大于栈中的,直接放到符号栈
            operatorStack.push(c);
          } else { // 当前的运算符优先级不大于栈中的优先级,从数栈中取出两个数,从符号栈中取出一个符号,进行计算. 并将结果放到数栈中,当前运算符放到符号栈中
            num1 = numStack.pop();
            num2 = numStack.pop();
            operator = operatorStack.pop();
            res = numStack.cal(num1, num2, operator);
            numStack.push(res);
            operatorStack.push(c);
          }
        }
      } else { // 是数字,放到数栈中,char与数字的转化,要减去48

        // 如此直接操作,只能计算位数为1的正整数
        // numStack.push(c - 48);

        keepNum += c;

        // 如果c已经是表达式最后一位,直接入栈
        if (index == expression.length() - 1) {
          numStack.push(Integer.parseInt(keepNum));
        } else { // 如果当前操作对象不是最后一位
          if (operatorStack.isOperator(expression.substring(index + 1, index + 2).charAt(0))) {
            // 下一位是运算符,当前结果可入栈
            numStack.push(Integer.parseInt(keepNum));
            // 清空keepNum
            keepNum = "";
          }
        }
      }

      // 循环遍历表达式的每一个值
      index++;
      // 直到最后,循环结束
      if (index == expression.length()) {
        break;
      }
    }

    // 表达式循环完毕,此时计算数栈和符号栈中的字符.
    while (true) {
      if (operatorStack.isEmpty()) {
        // 如果符号栈为空,则数栈中仅剩一个数字,即为结果
        break;
      }
      num1 = numStack.pop();
      num2 = numStack.pop();
      operator = operatorStack.pop();
      res = numStack.cal(num1, num2, operator);
      numStack.push(res);
    }

    System.out.printf("表达式 %s 的结果为 %d", expression, numStack.pop());

  }
}

/**
 * 表示栈
 */
class ArrayStack2 {

  /**
   * 栈的大小
   */
  private int maxSize;

  /**
   * 数组模拟栈
   */
  private int[] stack;

  /**
   * top代表栈顶,初始化为-1
   */
  private int top = -1;

  public ArrayStack2(int maxSize) {
    this.maxSize = maxSize;
    stack = new int[maxSize];
  }

  /**
   * 判断栈是否已经满了
   */
  public boolean isFull() {
    return top == maxSize - 1;
  }

  /**
   * 判断栈是否是空的
   */
  public boolean isEmpty() {
    return top == -1;
  }

  /**
   * 入栈
   */
  public void push(int value) {
    if (!isFull()) {
      top++;
      stack[top] = value;
    } else {
      System.out.println("栈满了");
    }
  }

  /**
   * 出栈
   */
  public int pop() {
    if (!isEmpty()) {
      int value = stack[top];
      top--;
      return value;
    } else {
      throw new RuntimeException("栈是空的");
    }
  }

  /**
   * 查看栈顶的值,不取出
   */
  public int peek() {
    return stack[top];
  }

  /**
   * 遍历栈
   */
  public void list() {
    // 遍历要从栈顶开始向栈底
    if (isEmpty()) {
      for (int i = top; i >= 0; i--) {
        System.out.printf("stack[%d] = %d \n", i, stack[i]);
      }
    } else {
      System.out.println("栈是空的");
    }
  }

  /**
   * 返回运算符的优先级,自己定义
   * 数字越大,代表优先级越高
   *
   * @param operator 运算符 + - * /
   */
  public int priority(int operator) {
    if (operator == '*' || operator == '/') {
      return 1;
    } else if (operator == '+' || operator == '-') {
      return 0;
    } else {
      return -1;
    }
  }

  /**
   * 判断字符是不是运算符
   *
   * @param val 公式中的字符
   */
  public boolean isOperator(char val) {
    return val == '+' || val == '-' || val == '*' || val == '/';
  }

  /**
   * 进行计算
   *
   * @param num1     后面的数字
   * @param num2     前面的数字
   * @param operator 运算符
   * @return 计算结果
   */
  public int cal(int num1, int num2, int operator) {
    // 计算结果
    int res = 0;

    switch (operator) {
      case '+':
        res = num1 + num2;
        break;
      case '-':
        res = num2 - num1;
        break;
      case '*':
        res = num1 * num2;
        break;
      case '/':
        res = num2 / num1;
        break;
      default:
        break;
    }
    return res;
  }
}
```



## 逆波兰表达式(后缀表达式)<div id="reverse-polish"></div>

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Stack;

/**
 * 逆波兰表达式(后缀表达式)
 *
 * @author 王越洋
 */
public class InversePolishExpression {

  public static void main(String[] args) {

    // String infixExpression = "(30 + 4) * 5 - 6";
    String infixExpression = "1+((2+3)*4)-5";

    // "(30 + 4) * 5 - 6" => [(, 30, +, 4, ), *, 5, -, 6]
    List<String> infixExpressionList = getInfixExpressionList(infixExpression);

    System.out.println(infixExpressionList);

    List<String> suffixExpressionList = parseSuffixExpressionList(infixExpressionList);

    System.out.println(suffixExpressionList);

    int cal = cal(suffixExpressionList);

    System.out.println(cal);

    // 逆波兰表达式 (30 + 4) * 5 - 6  =>  30 4 + 5 * 6 -
    // String suffixExpression = "30 4 + 5 * 6 -";
    //
    // List<String> suffixExpressionList = getStringList(suffixExpression);
    //
    // int res = cal(suffixExpressionList);
    // System.out.println(res);
  }

  /**
   * 将中缀表达式转成List
   *
   * @param expression 中缀表达式
   * @return List
   */
  private static List<String> getInfixExpressionList(String expression) {
    List<String> suffixExpressionList = new ArrayList<>();
    char c;
    int index = 0;
    do {
      if ((c = expression.charAt(index)) < 48 || (c = expression.charAt(index)) > 57) {
        // 该字符不是数字,跳过空格
        if (c != ' ') {
          suffixExpressionList.add(String.valueOf(c));
        }
        index++;
      } else {
        // 当前字符是数字
        String temp = "";

        while (index < expression.length() && expression.charAt(index) >= 48 &&
            (c = expression.charAt(index)) <= 57) {
          temp += c;
          index++;
        }
        suffixExpressionList.add(temp);
      }
    } while (index < expression.length());

    return suffixExpressionList;
  }

  /**
   * 将中缀表达式List转换成后缀表达式List
   *
   * @param infixExpressionList 中缀表达式List
   * @return 后缀表达式List
   */
  public static List<String> parseSuffixExpressionList(List<String> infixExpressionList) {
    /*
    定义两个栈: s1,s2,分别用以存放运算符和中间结果
    因为s2的内容只增不减,后面还需要反转得到最终的后缀表达式,所以直接使用List
     */
    Stack<String> stack = new Stack<>();
    List<String> suffixExpressionList = new ArrayList<>();

    for (String item : infixExpressionList) {
      // [(, 30, +, 4, ), *, 5, -, 6]

      // 判断当前字符是什么, 分为括号\数字\运算符
      if (item.equals("(")) {
        // 左括号直接压入s1
        stack.push(item);
      } else if (item.equals(")")) {
        // 如果是右括号,以此淡出s1中的运算符,直到遇到左括号为止. 丢弃掉这对括号
        String pop;
        while (!(pop = stack.pop()).equals("(")) {
          suffixExpressionList.add(pop);
        }
      } else if (item.matches("\\d+")) {
        // 如果是数字,直接压入s2, 即list
        suffixExpressionList.add(item);
      } else {
        // 遇到运算符,比较其与栈顶的运算符的优先级
        if (stack.isEmpty() || stack.peek().equals("(")) {
          // 如果s1为空,或者栈顶运算符为左括号,直接将此运算符放入栈中
          stack.push(item);
        } else {
          while ((!stack.isEmpty() && !stack.peek().equals("(")) &&
              (!comparePriority(item, stack.peek()))) {
            suffixExpressionList.add(stack.pop());
          }
          stack.push(item);
        }
      }
    }
    while (!stack.empty()) {
      suffixExpressionList.add(stack.pop());
    }
    return suffixExpressionList;
  }

  /**
   * 比较当前运算符是否比栈顶运算符的优先级大
   *
   * @param item 当前运算符
   * @param top  栈顶运算符
   * @return true: 当前运算符的优先级较大; false: 栈顶运算符的优先级大于等于当前运算符
   */
  private static boolean comparePriority(String item, String top) {
    if (item.equals("*") || item.equals("/")) {
      return top.equals("+") || top.equals("-");
    }
    return false;
  }

  /**
   * 循环每个元素,进行结果的计算
   *
   * @param stringList 表达式各个元素
   * @return 计算结果
   */
  private static int cal(List<String> stringList) {

    Stack<String> stack = new Stack<>();
    int res = 0;

    for (String item : stringList) {
      // 判断item是数字还是运算符
      if (item.matches("\\d+")) {
        // item是数字,将其放入栈中
        stack.push(item);
      } else {
        // item是运算符,从栈中取出两个数字,与此运算符进行计算
        int num2 = Integer.parseInt(stack.pop());
        int num1 = Integer.parseInt(stack.pop());

        switch (item) {
          case "+":
            res = num1 + num2;
            break;
          case "-":
            res = num1 - num2;
            break;
          case "*":
          case "x":
            res = num1 * num2;
            break;
          case "/":
          case "÷":
            res = num1 / num2;
            break;
          default:
            throw new RuntimeException("运算符暂不支持");
        }
        // 再将计算结果放入栈中
        stack.push(String.valueOf(res));
      }
    }
    return res;
  }

  /**
   * 将表达式按空格分成字符串集合
   *
   * @param suffixExpression 逆波兰表达式
   */
  private static List<String> getStringList(String suffixExpression) {
    String[] s = suffixExpression.split(" ");
    List<String> stringList = new ArrayList<>();
    Collections.addAll(stringList, s);
    return stringList;
  }

}
```

