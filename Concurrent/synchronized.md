# synchronized

## 对象锁

### 代码块形式

手动指定锁对象

#### 两个不同的对象锁

* this
* 自己定义的对象锁，比如`Object lock = new Object()`

代码

```java
package threadcoreknowledge.sync.objectlock;

/**
 * @author mfh
 * @date 2020/5/3 22:19
 */
public class SyncBlockTwoLockObject implements Runnable {
    private Object lock0 = new Object();
    private Object lock1 = new Object();

    @Override
    public void run() {
        synchronized (lock0) {
            System.out.println("我是 lock0，我叫：" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " lock0 部分运行结束");
        }

        synchronized (lock1) {
            System.out.println("我是 lock1，我叫：" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " lock1 部分运行结束");
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        SyncBlockTwoLockObject tlo = new SyncBlockTwoLockObject();
        Thread t0 = new Thread(tlo);
        Th
        Thread t1 = new Thread(tlo);
        t0.start();
        t1.start();
        t0.join();
        t1.join();
    }
}
```

```java
我是 lock0，我叫：Thread-0
Thread-0 lock0 部分运行结束
我是 lock1，我叫：Thread-0
我是 lock0，我叫：Thread-1
Thread-0 lock1 部分运行结束
Thread-1 lock0 部分运行结束
我是 lock1，我叫：Thread-1
Thread-1 lock1 部分运行结束
```

执行顺序分析：

t0 先执行并获取到 lock0，`我是 lock0，我叫：Thread-0`先被打印，3秒之后打印`Thread-0 lock0 部分运行结束`，之后 t0 线程获取到 lock1，打印`我是 lock1，我叫：Thread-0`，注意，由于 t0 释放了 lock0，所以 t0 线程获取到 lock1 的动作与 t1 获取到 lock0 的动作是并行执行的（lock0 与 lock1 是不同的对象），所以`我是 lock1，我叫：Thread-0`，`我是 lock0，我叫：Thread-1`几乎是同时打印的。之后`Thread-0 lock1 部分运行结束`，`Thread-1 lock0 部分运行结束`几乎也是同时打印的，因为不同的线程，不同的锁对象，各自执行各自同步块中的代码。当 t0 释放了 lock1 锁之后，t1 才能获取到 lock1 锁，然后打印`我是 lock1，我叫：Thread-1`，3秒后打印`Thread-1 lock1 部分运行结束`。执行顺序在某些地方是并行执行

将第二个同步代码块的锁也改成 lock0，和第一个同步代码块使用同一个对象锁，代码的执行结果如下：

```java
我是 lock0，我叫：Thread-0
Thread-0 lock0 部分运行结束
我是 lock1，我叫：Thread-0
Thread-0 lock1 部分运行结束
我是 lock0，我叫：Thread-1
Thread-1 lock0 部分运行结束
我是 lock1，我叫：Thread-1
Thread-1 lock1 部分运行结束
```

由结果得知，由于两个线程使用的同一个对象锁，所以执行结果是 t0 线程先执行完所有的代码，然后才会轮到 t1 线程执行。执行顺序是顺序执行。

由以上的例子可以看出，使用的锁不同，执行的顺序是不一样的。

### 方法锁形式

synchronized 修饰普通方法，锁对象默认为 this

```java
package threadcoreknowledge.sync.objectlock;

/**
 * @author mfh
 * @date 2020/5/3 22:09
 */
public class SyncMethod implements Runnable {
    private static int num = 0;
    @Override
    public void run() {
        calculate();
    }

    public synchronized void calculate() {
        for (int i = 0; i < 1000_00; i++) {
            num++;
        }
    }
    public static void main(String[] args) {
        SyncMethod sd = new SyncMethod();
        Thread t0 = new Thread(sd);
        Thread t1 = new Thread(sd);
        t0.start();
        t1.start();
        /*t0.join();
        t1.join();*/
        while (t0.isAlive() || t1.isAlive()) {}
        System.out.println(num);
    }
}
```

`while (t0.isAlive() || t1.isAlive()) {}`这种写法代替了 `t0.join();t1.join();`，同样能起到等待的效果。

## 类锁

概念：Java 类可能有很多个对象，但**只有1个 Class 对象**

本质：所谓的类锁，不过是 Class 对象的锁而已。

用法和效果：类锁只能在同一时刻被一个对象拥有

由于 Class 对象的锁而已，不同的线程使用的是同一把锁，所以会互斥，即同一时刻只能有一个线程访问被类所锁住的代码。

### static 静态方法

代码如下：

```java
package threadcoreknowledge.sync.classlock;

import java.util.concurrent.TimeUnit;

/**
 * 类锁的一种形式：static 同步方法
 * @author mfh
 * @date 2020/5/3 23:17
 */
public class SyncClassStatic implements Runnable {
    static SyncClassStatic syncClassStatic0 = new SyncClassStatic();
    static SyncClassStatic syncClassStatic1 = new SyncClassStatic();
    @Override
    public void run() {
        try {
            method();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    static synchronized void method() throws InterruptedException {
        System.out.println("我是类锁的第一种形式：static 形式，我叫：" + Thread.currentThread().getName());
        TimeUnit.SECONDS.sleep(3);
        System.out.println(Thread.currentThread().getName() + "运行结束");
    }

    public static void main(String[] args) throws InterruptedException {
        // syncClassStatic0、syncClassStatic1 的声明位置无所谓，静态成员变量与局部变量效果是一样的
        /*SyncClassStatic syncClassStatic0 = new SyncClassStatic();
        SyncClassStatic syncClassStatic1 = new SyncClassStatic();*/
        Thread t0 = new Thread(syncClassStatic0);
        Thread t1 = new Thread(syncClassStatic1);
        t0.start();
        t1.start();
        t0.join();
        t1.join();
    }
}

```

结果：

```java
我是类锁的第一种形式：static 形式，我叫：Thread-1
我是类锁的第一种形式：static 形式，我叫：Thread-0
Thread-0运行结束
Thread-1运行结束
```

`我是类锁的第一种形式：static 形式，我叫：Thread-1`，`我是类锁的第一种形式：static 形式，我叫：Thread-0`同时打印；`Thread-0运行结束`，`Thread-1运行结束`同时打印。根本没有做到同步执行。将 method 方法加上 `static`，也就是将其变成静态方法，`static synchronized void method()`，就能串行执行。

t0 先执行，打印`我是类锁的第一种形式：static 形式，我叫：Thread-1`，等3秒打印`Thread-0运行结束`，然后 t1 才会执行，之后打印其他的。

### synchronized (*.class) 代码块

```java
package threadcoreknowledge.sync.classlock;

import java.util.concurrent.TimeUnit;

/**
 * 类锁的一种形式：synchronized(*.class)
 * @author mfh
 * @date 2020/5/4 9:29
 */
public class SyncClassClass implements Runnable {
    static SyncClassStatic syncClassStatic0 = new SyncClassStatic();
    static SyncClassStatic syncClassStatic1 = new SyncClassStatic();
    @Override
    public void run() {
        try {
            synchronized (SyncClassClass.class) {
                System.out.println("我是类锁的第一种形式：static 形式，我叫：" + Thread.currentThread().getName());
                TimeUnit.SECONDS.sleep(3);
                System.out.println(Thread.currentThread().getName() + "运行结束");
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t0 = new Thread(syncClassStatic0);
        Thread t1 = new Thread(syncClassStatic1);
        t0.start();
        t1.start();
        t0.join();
        t1.join();
    }
}
```

执行效果和上面使用`static synchronized void method()`一致。

## 性质 

### 可重入

* 什么是可重入：指的是同一线程的外层函数获得锁之后，内层函数可以直接再次获取该锁。
* 好处：避免死锁、提升封装性
* 粒度：线程而非调用（用3种情况来说明和pthread的区别）
  * 情况1：证明同一个方法是可重入的
  * 情况2：证明可重入不要求是同一个方法
  * 情况3：证明可重入不要求是同一个类中的

#### 证明同一个方法是可重入的

```java
package threadcoreknowledge.sync;

/**
 * 可重入粒度测试：递归调用本方法
 * @author mfh
 * @date 2020/5/4 9:45
 */
public class SynchronizedRecursive {
    int num;
    public synchronized void method() {
        System.out.println("执行 method，num：" + num);
        if (num == 0) {
            num++;
            this.method();
        }
    }
    public static void main(String[] args) {
        SynchronizedRecursive sr = new SynchronizedRecursive();
        sr.method();
    }
}
```

#### 证明可重入不要求是同一个方法

```java
package threadcoreknowledge.sync;

/**
 * 可重入粒度测试：调用类内另外的方法
 * @author mfh
 * @date 2020/5/4 9:45
 */
public class SynchronizedRecursive {
    public synchronized void method0() {
        System.out.println("执行 method0");
        this.method1();
    }

    public synchronized void method1() {
        System.out.println("执行 method1");
    }
    public static void main(String[] args) {
        SynchronizedRecursive sr = new SynchronizedRecursive();
        sr.method0();
    }
}
```

#### 证明可重入不要求是同一个类中的

```java
package threadcoreknowledge.sync;

/**
 * 可重入粒度测试：调用父类的方法
 * @author mfh
 * @date 2020/5/4 9:45
 */
public class SynchronizedBase {
    public synchronized void method() {
        System.out.println("我是父类方法 method");
    }
}

class SynchronizedChild extends SynchronizedBase {
    public synchronized void method() {
        System.out.println("我是子类方法 method");
        super.method();
    }

    public static void main(String[] args) {
        SynchronizedBase sc = new SynchronizedChild();
        sc.method();
    }
}
```

#### 结论

经过以上三点验证，证明在 java 中，sychronized 关键字它的粒度范围不是调用层面的。而是线程层面的，在同一个线程中如果已经拿到了一把锁，然后又想接着使用这把锁去访问其他的方法，或者其他类的方法，只要它需要的锁仍然是手中的这把锁，那么可重入性质就会被激发出来，就不在需要显式的释放锁与重新获取锁，它可以直接去完成这件事情。

### 不可中断

一旦这个锁已经被别人获得了，如果我还想获得，我只能选择等待或者阻塞，直到别的线程<font color=red>**释放**</font>这个锁。如果别的线程永远不释放锁，那么我只能永远的等待下去。

相比之下，Lock 类，可以拥有中断的能力。第一点，如果我觉得我等的时间太长了，有权中断现在已经获取到的锁的线程的执行；第二点，如果我觉得我等待的时间太长了不想等了，也可以退出。

## 深入原理

### 加锁和释放锁的原理