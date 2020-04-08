# Synchronized

synchronized代码块是对对象进行加锁操作，那么它是如何保障synchronized代码块中如果出现了问题，对此对象做出正确的解锁操作呢？也就是加锁解锁的操作是要成对出现的，如果加了锁，将来出现了问题，没有正确解锁的话，那么这个对象也就没其他线程可以访问了。下面就从字节码的角度解释这个问题。

## 源码

```java
public class Demo21 {
    public static void main(String[] args) {
        Object lock = new Object();
        synchronized (lock) {
            System.out.println("ok");
        }
    }
}
```

## 反编译后的字节码

```java
public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=1
         0: new           #2                  // class java/lang/Object
         3: dup
         4: invokespecial #1                  // Method java/lang/Object."<init>":()V
         7: astore_1
         8: aload_1
         9: dup
        10: astore_2
        11: monitorenter
        12: getstatic     #3                  // Field java/lang/System.out:Ljava/io/PrintStream;
        15: ldc           #4                  // String ok
        17: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        20: aload_2
        21: monitorexit
        22: goto          30 
        25: astore_3
        26: aload_2
        27: monitorexit
        28: aload_3
        29: athrow
        30: return
      Exception table:
         from    to  target type
            12    22    25   any
            25    28    25   any
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      31     0  args   [Ljava/lang/String;
            8      23     1  lock   Ljava/lang/Object;
```

### new

new 对象，会将对象的引用放入操作数栈

### dup

把对象的引用复制了一份，复制了一份是为了下面两次使用

### invokespecial #1

执行`Object`的构造方法（消耗一个对象引用）

### astore_1

将对象引用存入局部变量表1号槽位（又消耗了一个对象引用），也就是`lock`引用

以上指令对应源码：`Object lock = new Object();`

### aload_1

将局部变量表1号槽位的对象引用压入操作数栈（`synchronized`开始）

### dup

把`lock`对象的引用复制了一份

其中一个是给`monitorenter`指令使用，另外一个是`monitorexit`指令用的，即一个是加锁用的，一个是解锁用的，加锁与解锁分别会消耗一个对象引用（肯定得是同一个对象引用）

### astore_2

将`lock`对象引用存入局部变量表`2`号槽位（消耗了一个lock对象引用，还剩一个）（没有名字的槽位）

### monitorenter

对`lock`引用指向的对象做了加锁操作（消耗一个`lock`对象引用，操作数栈里没有lock对象引用了）

锁住以后就可以安全的执行`synchronized`代码块中的代码了

### getstatic     #3

System.out

### ldc   #4

将运行时常量池4号位置的值压入操作数栈，即`ok`字符串

### invokevirtual #5

执行`println`方法

>getstatic     #3，ldc   #4，invokevirtual #5
>
>以上指令对应源码：`System.out.println("ok");`

### aload_2

将局部变量表2号槽位的lock对象引用压入操作数栈

### monitorexit

执行解锁操作（必须是对同一个对象的引用解锁，也就是`lock`对象引用）

加锁与解锁操作的是同一对象的引用，所以会成功解锁

>aload_2、monitorexit
>
>以上指令的执行时机是没有发生异常的情况

### goto  30

由于没有发生异常，跳转到30行

### return

程序结束

---

### 12~21行发生了异常的情况

由异常表得知，如果在同步块中发生了异常，则会跳转到25行；如果在`catch`块（25~27行）中发生了异常，也会跳转到25行。

### astore_3

将异常对象存储到局部变量表3号槽位

### aload_2

将局部变量表2号槽位的lock对象引用压入操作数栈

### monitorexit

执行解锁操作（必须是对同一个对象的引用解锁，也就是`lock`对象引用）

### aload_3

加载异常对象引用

### athrow

抛出异常

### return

程序结束

通过分析字节码得知，无论发生异常，还是不发生异常，都会正确执行解锁操作

**注意，方法级别的 synchronized 不会在字节码指令中有所体现**。

