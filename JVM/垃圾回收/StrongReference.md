# Strong reference

对象只要有引用指向它，即使垃圾回收，也不会将其清除，如果强引用对象越来越多，势必会造成堆内存溢出

## 示例

```java
// -Xmx20m -XX:+PrintGCDetails -verbose:gc
public class StrongRefDemo {
    private final static int FOUR_MB = 1024 * 1024 * 4;
    private static void strongRef() {
        List<byte[]> list = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            byte[] bytes = new byte[FOUR_MB];
            list.add(bytes);
            System.out.println(bytes);
        }
    }

    public static void main(String[] args) {
        strongRef();
    }
}
```

```java
[B@14ae5a5
[B@7f31245a
[B@6d6f6e28
[GC (Allocation Failure) [PSYoungGen: 2162K->496K(6144K)] 14450K->12984K(19968K), 0.0015880 secs] [Times: user=0.03 sys=0.02, real=0.00 secs] 
[B@135fbaa4
[GC (Allocation Failure) --[PSYoungGen: 4704K->4704K(6144K)] 17192K->17200K(19968K), 0.0027848 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Ergonomics) [PSYoungGen: 4704K->4567K(6144K)] [ParOldGen: 12496K->12475K(13824K)] 17200K->17042K(19968K), [Metaspace: 3464K->3464K(1056768K)], 0.0064357 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[GC (Allocation Failure) --[PSYoungGen: 4567K->4567K(6144K)] 17042K->17082K(19968K), 0.0008743 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 4567K->4522K(6144K)] [ParOldGen: 12515K->12502K(13824K)] 17082K->17024K(19968K), [Metaspace: 3464K->3464K(1056768K)], 0.0048753 secs] [Times: user=0.05 sys=0.00, real=0.01 secs] 
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at gc.StrongRefDemo.strongRef(StrongRefDemo.java:15)
	at gc.StrongRefDemo.main(StrongRefDemo.java:22)
```

代码执行过程中，循环5次，每次往集合中添加大小为`4MB`的字节数组。最终集合中存放的数组大小为`20m`，集合对象由`GC Root`强引用（方法没有结束），不能被垃圾回收，既然集合不能被垃圾回收，集合中元素就会一直存在，最终造成了堆内存溢出。

> 如果字节数组没有放入集合中，就不会导致内存溢出，原因参见:[https://github.com/myz2016/blog/blob/master/JVM/%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6/%E5%88%86%E6%9E%90%E5%A6%82%E4%BD%95%E5%86%99%E4%BB%A3%E7%A0%81%E8%83%BD%E5%AF%BC%E8%87%B4%E5%86%85%E5%AD%98%E6%BA%A2%E5%87%BA.md](https://github.com/myz2016/blog/blob/master/JVM/垃圾回收/分析如何写代码能导致内存溢出.md)