# 并发编程

---

待完善

<!--more-->

----------

## CountDownLatch

`CountDownLatchTest.java`

```java
import java.util.concurrent.CountDownLatch;

/**
 * public void await() throws InterruptedException { };   // 调用await()方法的线程会被挂起，它会等待直到count值为0才继续执行
 * public boolean await(long timeout, TimeUnit unit) throws InterruptedException { };  // 和await()
 * 类似，只不过等待一定的时间后count值还没变为0的话就会继续执行
 * public void countDown() { };  // 将count值减1
 */
public class CountDownLatchTest {

    public static void main(String[] args) {

        final CountDownLatch latch = new CountDownLatch(2);

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "正在执行");
            try {
                Thread.sleep(3500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "执行完毕");
            latch.countDown();
        }).start();

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "正在执行");
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "执行完毕");
            latch.countDown();
        }).start();

        try {
            System.out.println("main 线程 正在执行");
            latch.await();
            System.out.println("main 线程 执行完毕");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## CyclicBarrier

`CyclicBarrierTest.java`

```java
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class CyclicBarrierTest {

    public static void main(String[] args) {
        final int N = 4;
        CyclicBarrier barrier = new CyclicBarrier(N, new Runnable() {
            @Override
            public void run() {
                System.out.println("所有线程执行完毕后的行为，由" + Thread.currentThread().getName() + "执行");
            }
        });

        System.out.println("多线程写入");
        for (int i = 0; i < N; i++) {
            new Writer(barrier).start();
        }

    }

    static class Writer extends Thread {
        private CyclicBarrier cyclicBarrier;

        public Writer(CyclicBarrier cyclicBarrier) {
            this.cyclicBarrier = cyclicBarrier;
        }

        @Override
        public void run() {
            System.out.println("线程" + Thread.currentThread().getName() + "正在写入数据...");
            try {
                Thread.sleep(5000);      // 以睡眠来模拟写入数据操作
                System.out.println("线程" + Thread.currentThread().getName() + "写入数据完毕，等待其他线程写入完毕");
                cyclicBarrier.await();
            } catch (InterruptedException | BrokenBarrierException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## Semaphore

`SemaphoreTest.java`

```java
import java.util.concurrent.Semaphore;

public class SemaphoreTest {

    public static void main(String[] args) {
        final int N = 8;
        Semaphore semaphore = new Semaphore(5);

        for (int i = 0; i < N; i++) {
            new Worker(i, semaphore).start();
        }
    }

    static class Worker extends Thread {
        private int num;
        private Semaphore semaphore;

        public Worker(int num, Semaphore semaphore) {
            this.num = num;
            this.semaphore = semaphore;
        }

        @Override
        public void run() {
            try {
                semaphore.acquire();
                System.out.println("工人" + this.num + "占用一个机器在生产...");
                Thread.sleep(2000);
                System.out.println("工人" + this.num + "释放出机器");
                semaphore.release();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```