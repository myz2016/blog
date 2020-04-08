# Weak reference

* 仅有弱引用引用该对象时，在垃圾回收时，无论内存是否充足，都会回收弱引用对象
* 可以配合引用队列来释放弱引用自身

```java
public class Demo1 {
    private static final int _4MB = 4 * 1024 * 1024;

    public static void main(String[] args) {
        //  list --> WeakReference --> byte[]
        List<WeakReference<byte[]>> list = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            WeakReference<byte[]> ref = new WeakReference<>(new byte[_4MB]);
            list.add(ref);
            for (WeakReference<byte[]> w : list) {
                System.out.print(w.get()+" ");
            }
            System.out.println();

        }
        System.out.println("循环结束：" + list.size());
    }
}
```

```java
[B@14ae5a5 
[B@14ae5a5 [B@7f31245a 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 
[GC (Allocation Failure) [PSYoungGen: 2162K->488K(6144K)] 14450K->13024K(19968K), 0.0015414 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 [B@135fbaa4 
[GC (Allocation Failure) [PSYoungGen: 4696K->480K(6144K)] 17232K->13040K(19968K), 0.0011420 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null [B@45ee12a7 
循环结束：5
```

由输出结果得知，集合中加入第四个字节数组对象之前，发生了`Minor GC`，但幸运的是，没有对象被回收；当集合中加入第五个字节数组对象之前，发生了`Minor GC`，并且第四个对象被回收了。

## 添加6个对象的结果输出

```java
[B@14ae5a5 
[B@14ae5a5 [B@7f31245a 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 
[GC (Allocation Failure) [PSYoungGen: 2162K->488K(6144K)] 14450K->13016K(19968K), 0.0017131 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 [B@135fbaa4 
[GC (Allocation Failure) [PSYoungGen: 4696K->472K(6144K)] 17224K->13000K(19968K), 0.0086439 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null [B@45ee12a7 
[GC (Allocation Failure) [PSYoungGen: 4679K->504K(6144K)] 17207K->13032K(19968K), 0.0007512 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null null [B@330bedb4 
循环结束：6
```

由以上结果得知，第6个对象被添加进集合之前，由于发生了垃圾回收，将第5个对象回收了。

## 添加10个对象的结果输出

```java
[B@14ae5a5 
[B@14ae5a5 [B@7f31245a 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 
[GC (Allocation Failure) [PSYoungGen: 2162K->488K(6144K)] 14450K->13000K(19968K), 0.0015797 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 [B@135fbaa4 
[GC (Allocation Failure) [PSYoungGen: 4696K->504K(6144K)] 17208K->13016K(19968K), 0.0009675 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null [B@45ee12a7 
[GC (Allocation Failure) [PSYoungGen: 4711K->488K(6144K)] 17223K->13016K(19968K), 0.0108694 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null null [B@330bedb4 
[GC (Allocation Failure) [PSYoungGen: 4694K->488K(6144K)] 17222K->13016K(19968K), 0.0005897 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null null null [B@2503dbd3 
[GC (Allocation Failure) [PSYoungGen: 4694K->488K(6144K)] 17222K->13024K(19968K), 0.0006493 secs] [Times: user=0.05 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null null null null [B@4b67cf4d 
[GC (Allocation Failure) [PSYoungGen: 4694K->504K(5120K)] 17230K->13048K(18944K), 0.0005514 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null null null null null [B@7ea987ac 
[GC (Allocation Failure) [PSYoungGen: 4690K->32K(5632K)] 17234K->13032K(19456K), 0.0006083 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Ergonomics) [PSYoungGen: 32K->0K(5632K)] [ParOldGen: 13000K->659K(8192K)] 13032K->659K(13824K), [Metaspace: 3466K->3466K(1056768K)], 0.0076259 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
null null null null null null null null null [B@12a3a380 
循环结束：10
```

前几次就不看了，主要关注添加第9~10个对象的结果输出

```java
[B@14ae5a5 [B@7f31245a [B@6d6f6e28 null null null null null [B@7ea987ac 
[GC (Allocation Failure) [PSYoungGen: 4690K->32K(5632K)] 17234K->13032K(19456K), 0.0006083 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Ergonomics) [PSYoungGen: 32K->0K(5632K)] [ParOldGen: 13000K->659K(8192K)] 13032K->659K(13824K), [Metaspace: 3466K->3466K(1056768K)], 0.0076259 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
null null null null null null null null null [B@12a3a380 
```

当第9个对象添加进集合后，集合中只剩下了4个对象，`4m * 4m = 16m`，如果再放一个对象进来，差不多也能放下。但是当放第10个对象的时候，发生了`Full GC`，其实也可以理解，软引用对象本身也会占用一些内存，所以加上第10个对象，内存就不够了，最终集合就剩下了一个对象，前4个全部在`Full GC`的时候被回收了。