# sleep方法详解

* 作用：只想让线程在预期的时间执行，其他时候不要占用 CPU 资源。

不释放锁

* 包括 synchronized 这种 monitor 锁和 lock 锁
* 和 wait 不同

代码演示：

### sleep 不会释放 monitor 锁

```java
package threadcoreknowledge.threadobjectclasscommonmethods;

/**
 * sleep 不会释放 monitor 锁
 * @author mfh
 * @date 2020/4/26 13:43
 */
public class SleepDontReleaseMonitor implements Runnable {

    @Override
    public void run() {
        this.sync();
    }

    private synchronized void sync() {
        System.out.println(Thread.currentThread().getName() + " 获取到了 monitor 锁！");
        try {
            Thread.sleep(5000);
            System.out.println(Thread.currentThread().getName() + " 苏醒了！");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        SleepDontReleaseMonitor demo = new SleepDontReleaseMonitor();
        Thread t0 = new Thread(demo);
        Thread t1 = new Thread(demo);
        t0.start();
        t1.start();
    }
}
```

### sleep 不会释放 lock 锁

lock 锁需要手动释放

```java
package threadcoreknowledge.threadobjectclasscommonmethods;

import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

/**
 * sleep 不会释放 lock 锁（lock 锁需要手动释放）
 * @author mfh
 * @date 2020/4/26 13:47
 */
public class SleepDontReleaseLock implements Runnable {
    private static final Lock LOCK = new ReentrantLock();

    @Override
    public void run() {
        LOCK.lock();
        try {
            System.out.println(Thread.currentThread().getName() + " 获取到了 lock 锁！");
            Thread.sleep(5000);
            System.out.println(Thread.currentThread().getName() + " 已经苏醒！");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            LOCK.unlock();
        }
    }
    
    public static void main(String[] args) {
        SleepDontReleaseLock demo = new SleepDontReleaseLock();
        Thread t0 = new Thread(demo);
        Thread t1 = new Thread(demo);
        t0.start();
        t1.start();
    }
}
```

以上两个例子的结果输出：

都是一个线程先获取到锁，然后等待5秒，然后苏醒，然后另一个线程在获取到锁，然后等待5秒，然后苏醒。

## sleep 方法响应中断

1. 抛出 InterruptedException
2. 清除中断状态

代码演示：

```java
package threadcoreknowledge.threadobjectclasscommonmethods.sleep;

import java.time.LocalTime;
import java.util.concurrent.TimeUnit;

/**
 * @author mfh
 * @date 2020/4/26 14:21
 */
public class SleepInterrupted implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            try {
                TimeUnit.SECONDS.sleep(1);
                System.out.println(LocalTime.now());
            } catch (InterruptedException e) {
                System.out.println("我被中断了！");
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        SleepInterrupted sleepInterrupted = new SleepInterrupted();
        Thread t = new Thread(sleepInterrupted);
        t.start();
        Thread.sleep(6500);
        t.interrupt();
    }
}
```

结果：

```java
14:25:32.295
14:25:33.296
14:25:34.296
14:25:35.296
14:25:36.296
14:25:37.296
我被中断了！
java.lang.InterruptedException: sleep interrupted
	at java.lang.Thread.sleep(Native Method)
	at java.lang.Thread.sleep(Thread.java:340)
	at java.util.concurrent.TimeUnit.sleep(TimeUnit.java:386)
	at threadcoreknowledge.threadobjectclasscommonmethods.sleep.SleepInterrupted.run(SleepInterrupted.java:15)
	at java.lang.Thread.run(Thread.java:748)
14:25:38.711
14:25:39.711
14:25:40.711
```

TimeUnit.SECOND.sleep(time) 比 Thread.sleep(time) 写法优雅，优雅点：

1. TimeUnit  可以指定 秒，时，分等各种时间单位，比如休眠1小时20分，可以写：

```java
TimeUnit.HOURS.sleep(1);
TimeUnit.MINUTES.sleep(20);
```

而 Thread.sleep(time) 需要自己计算1小时20分等于多少毫秒。

 	2. TimeUnit 对于小于 0 等于 0 的时间直接过滤，源码如下：

```java
public void sleep(long timeout) throws InterruptedException {
        if (timeout > 0) {
            long ms = toMillis(timeout);
            int ns = excessNanos(timeout, ms);
            Thread.sleep(ms, ns);
        }
    }
```

而 Thread.sleep 时间如果小于 0 则会抛出异常。

```java
public static void sleep(long millis, int nanos)
    throws InterruptedException {
        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }

        if (nanos >= 500000 || (nanos != 0 && millis == 0)) {
            millis++;
        }

        sleep(millis);
    }
```

## 一句话总结

sleep 方法可以让线程进入 Waiting 状态，并不会占用 CUP 资源，但是不释放锁，直到规定时间后再执行，休眠期间如果被中断，会抛出异常并清除中断状态。

## 面试问题

wait/notify、sleep 异同

* 相同点
  * 阻塞
  * 响应中断
* 不同点
  * 同步方法中
    * wait/notify 要在 synchronized 包裹的代码块中才可以调用
    * sleep 不需要
  * 释放锁
    * wait 会释放锁
    * sleep 不会释放锁
  * 指定时间
    * wait 可以不指定时间
    * sleep 必须指定时间
  * 所属类
    * wait/notify 所属 Object 类
    * sleep 所属 Thread 类