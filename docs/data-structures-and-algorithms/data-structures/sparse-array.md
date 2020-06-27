# 稀疏数组

## 概念

所谓稀疏数组就是数组中大部分的内容值都未被使用（或都为零），在数组中仅有少部分的空间使用。因此造成内存空间的浪费，为了节省内存空间，并且不影响数组中原有的内容值，我们可以采用一种压缩的方式来表示稀疏数组的内容。



假设有一个 9*7 的数组，内容如下

![9*7数组][1]

在此数组中，共有63个空间，但却只使用了5个元素，造成58个元素空间的浪费。以下我们就使用稀疏数组重新来定义这个数组：

![稀疏数组][2]

其中在稀疏数组中第一部分所记录的是原数组的列数和行数以及元素使用的个数、第二部分所记录的是原数组中元素的位置和内容。

需要说明，第二部分的元素列数是指数组下标的列数，跟第一部分的实际列数不相同

其中在稀疏数组中第一部分所记录的是原数组的列数和行数以及元素使用的个数、第二部分所记录的是原数组中元素的位置和内容。

例如第二部分内容可以表达为，假设数组为array\[]\[]， array\[1]\[1]=3，array\[3][0]=1...等等

经过压缩之后，原来需要声明大小为63的数组，而使用压缩后，只需要声明大小为6*3的数组，仅需18个存储空间。



## Java代码实现<div id="java"></div>

```java
/**
 * 稀疏数组 与 二维数组 之间的转换
 *
 * @author 王越洋
 */
public class SparseArray {

  public static void main(String[] args) {

    /*
     创建一个原始的二维数组 11 * 11
      0: 没有棋子
      1: 黑子
      2: 白子
     */
    int[][] chessArr1 = new int[11][11];

    chessArr1[1][2] = 1;
    chessArr1[2][3] = 2;
    chessArr1[5][7] = 2;
    chessArr1[4][7] = 9;

    // 原始的二维数组,打印
    for (int[] row : chessArr1) {
      for (int data : row) {
        System.out.printf("%d\t", data);
      }
      System.out.println();
    }
    System.out.println();

    /*
     将二维数组 转 稀疏数组
     */
    // 1.遍历二维数组,得到全部非0个数
    int sum = 0;
    for (int[] row : chessArr1) {
      for (int data : row) {
        if (data != 0) {
          sum++;
        }
      }
    }
    // 2.创建对应的稀疏数组
    int[][] sparseArr = new int[sum + 1][3];
    // 3.给稀疏数组赋值
    sparseArr[0][0] = chessArr1.length;
    sparseArr[0][1] = chessArr1[0].length;
    sparseArr[0][2] = sum;

    // 3-1.遍历原始二维数组,找到每个非0数据填入稀疏数组
    // 定义一个计数器,计算每个非0数据应该放入哪一行
    int count = 0;
    for (int i = 0; i < chessArr1.length; i++) {
      for (int j = 0; j < chessArr1[i].length; j++) {
        if (chessArr1[i][j] != 0) {
          count++;
          sparseArr[count][0] = i;
          sparseArr[count][1] = j;
          sparseArr[count][2] = chessArr1[i][j];
        }
      }
    }

    // 4.打印稀疏数组
    for (int[] row : sparseArr) {
      System.out.printf("%d\t%d\t%d\n", row[0], row[1], row[2]);
    }
    System.out.println();

    /*
    将稀疏数组 转 二维数组
     */
    // 1.根据稀疏数组的第一行,确定原始二维数组的大小
    int[][] chessArr2 = new int[sparseArr[0][0]][sparseArr[0][1]];

    // 2.将稀疏数组里面的非0只赋值给二维数组
    for (int i = 1; i < sparseArr.length; i++) {
      chessArr2[sparseArr[i][0]][sparseArr[i][1]] = sparseArr[i][2];
    }

    // 3.打印新生成的二维数组
    for (int[] row : chessArr2) {
      for (int data : row) {
        System.out.printf("%d\t", data);
      }
      System.out.println();
    }
  }
}
```



[1]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201910/194ff5b86d9e40959b9aca22bfcceb3d.jpg
[2]: https://blog-1252667810.cos.ap-shanghai.myqcloud.com/image/201910/a3b5161bd6f549fead3167a2ed04bf7e.jpg