# Thread和Object类中重要的方法详解

## 方法概览

| 类     | 方法名                    | 简介                                                         |
| ------ | ------------------------- | ------------------------------------------------------------ |
| Thread | sleep相关                 | 本表格的“相关”，指的重载方法，也就是方法名相同，但是参数不同，例如sleep有多个方法，只是参数不同，实际作用大同小异 |
|        | join                      | 等待其他线程执行完毕                                         |
|        | yield相关                 | 放弃已经获取到的cpu资源                                      |
|        | currentThread             | 获取当前执行线程的引用                                       |
|        | start，run相关            | 启动线程相关                                                 |
|        | interrupt相关             | 中断线程                                                     |
|        | stop，suspend，resume相关 | 已废弃                                                       |
| Object | wait/notify/notifyAll相关 | 让线程暂时休息和唤醒                                         |

## wait、notify、notifyAll作用及用法

*  阻塞阶段

调用 wait 方法后，线程就进入阻塞状态，直到以下4种情况之一发生时，才会被唤醒

1. 另一个线程调用这个对象的 notify() 方法且刚好被唤醒的是本线程
2. 另一个线程调用这个对象的 notifyAll() 方法
3. 过了 wait(long timeout) 规定超过的时间，如果传入0就是永久等待
4. 线程自身调用了 interrupt() 



* 唤醒阶段

notify() 会唤醒单个正在等待某对象 monitor 锁的线程，唤醒时如果有多个线程都在等待，它只会选取其中一个。它被唤醒之后，这个线程就不在是等待的状态，它会重新参与到线程的调度中，等到时机合适被调度进来，它就可以继续执行了。注意这里说的是等到时机合适被调度进来才会继续执行，而不是被唤醒后就立即执行。

notifyAll() 会唤醒所有正在等待某对象 monitor 锁的线程，唤醒之后至于那个线程会获取到锁，这要依赖于操作系统的调度。

wait() 与 notify()、notifyAll() 方法必须在 synchronized 修饰的代码块或方法中调用。



* 遇到中断

阻塞状态时遇到中断，会抛出 InterruptedException 异常，然后释放锁。

## 代码演示

### wait 演示

wait 演示参见 《线程状态分析》

### notify 和 notifyAll 演示

3个线程，t0和t1首先被阻塞，t2唤醒 notifyAll() 唤醒它们，代码如下：

```java
package threadcoreknowledge.threadobjectclasscommonmethods;

/**
 * 描述：3个线程，t0和t1首先被阻塞，t2唤醒 notifyAll() 唤醒它们。
 * start 先执行不代表先启动
 * @author mfh
 * @date 2020/4/24 12:17
 */
public class WaitNotifyAll implements Runnable {
    private static final Object resourceA = new Object();
    @Override
    public void run() {
        synchronized (resourceA) {
            System.out.println(Thread.currentThread().getName() + " 获取到资源锁！");
            try {
                System.out.println(Thread.currentThread().getName() + " 准备释放资源锁！");
                resourceA.wait();
                System.out.println(Thread.currentThread().getName() + " 再次获取到资源锁，即将结束同步代码块！");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    public static void main(String[] args) throws InterruptedException {
        WaitNotifyAll wna = new WaitNotifyAll();
        Thread t0 = new Thread(wna);
        Thread t1 = new Thread(wna);
        Thread t2 = new Thread(() -> {
            synchronized (resourceA) {
                System.out.println(Thread.currentThread().getName() + " 获取到资源锁！");
                System.out.println(Thread.currentThread().getName() + " 准备唤醒等待资源锁的线程");
                resourceA.notifyAll();
                System.out.println(Thread.currentThread().getName() + " 已发出唤醒信号，即将结束同步代码块！");
            }
        });
        t0.start();
        t1.start();
        Thread.sleep(200);
        t2.start();
    }
}
```

结果：

```java
Thread-0 获取到资源锁！
Thread-0 准备释放资源锁！
Thread-1 获取到资源锁！
Thread-1 准备释放资源锁！
Thread-2 获取到资源锁！
Thread-2 准备唤醒等待资源锁的线程
Thread-2 已发出唤醒信号，即将结束同步代码块！
Thread-1 再次获取到资源锁，即将结束同步代码块！
Thread-0 再次获取到资源锁，即将结束同步代码块！
```

如果将 `resourceA.notifyAll()` 换成 `resourceA.notify()`，最后将会有一个线程陷入无穷的等待，因为没有线程再去唤醒等待的线程，所以程序一直不会结束。 `resourceA.notify()`一次只会唤醒一个 wait 的线程。

如果将 `Thread.sleep(200)`去掉，程序也有可能陷入无限的等待。这也说明了先 start 不一定先执行。由于没有了休眠，也就不能保证 t2 线程最后执行，一旦 t2 线程先执行，则程序会陷入无穷的等待，因为没有线程去唤醒等待的线程，所以程序一直不会结束。

### 只释放当前 monitor 演示

代码如下：

```java
package threadcoreknowledge.threadobjectclasscommonmethods;

/**
 * 证明 wait 只释放当前的那把锁
 * @author mfh
 * @date 2020/4/24 15:15
 */
public class WaitNotifyReleaseOwnMonitor {
    private static final Object resourceA = new Object();
    private static final Object resourceB = new Object();
    public static void main(String[] args) {
        new Thread(() -> {
            synchronized (resourceA) {
                System.out.println(Thread.currentThread().getName() + " 获取到资源锁A！");
                synchronized (resourceB) {
                    System.out.println(Thread.currentThread().getName() + " 获取到资源锁B！");
                    try {
                        System.out.println(Thread.currentThread().getName() + " 释放资源锁A！");
                        resourceA.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();

        new Thread(() -> {
            try {
                // 等待是为了让 t0 先执行
                Thread.sleep(200);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (resourceA) {
                System.out.println(Thread.currentThread().getName() + " 获取到资源锁A！");
                System.out.println(Thread.currentThread().getName() + " 尝试获取资源锁B！");
                synchronized (resourceB) {
                    // 以下代码不会执行
                    System.out.println(Thread.currentThread().getName() + " 获取到资源锁B！");
                }
            }
        }).start();
    }
}
```

结果：

```java
Thread-0 获取到资源锁A！
Thread-0 获取到资源锁B！
Thread-0 释放资源锁A！
Thread-1 获取到资源锁A！
Thread-1 尝试获取资源锁B！
```

程序会陷入无穷等待。

如果一个线程持有多把锁，就需要注意获取的顺序还有释放的顺序，因为一不小心就会产生死锁的情况。

想获得哪把锁，想释放哪把锁，必须正确的把锁对应的对象给运用出来，每个锁都是独立的。

### wait，notify，notifyAll 特点、性质

1. 必须先拥有 monitor
2. 只能唤醒一个
3. 属于 Object 类
4. 类似功能的 Condition
5. 同时持有多个锁的情况