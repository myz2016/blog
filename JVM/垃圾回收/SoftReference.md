# Soft reference

* 仅有软引用引用该对象时，在垃圾回收后，内存仍不足时会再次出发垃圾回收，回收软引用
  对象
* 可以配合引用队列来释放软引用自身

## 软引用

### 示例

```java
public class Demo {
    private final static int _4mb = 4 * 1024 * 1024;
    public static void softRef() {
        List<SoftReference<byte[]>> list = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            SoftReference<byte[]> softReference = new SoftReference<>(new byte[_4mb]);
            list.add(softReference);
            System.out.println(softReference.get());
        }
        System.out.println("-----------------------------");
        for (SoftReference<byte[]> reference : list) {
            System.out.println(reference.get());
        }
    }

    public static void main(String[] args) {
        softRef();
    }
}
```

```java
[B@14ae5a5
[B@7f31245a
[B@6d6f6e28
[GC (Allocation Failure) [PSYoungGen: 2163K->488K(6144K)] 14451K->12988K(19968K), 0.0024174 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[B@135fbaa4
[GC (Allocation Failure) --[PSYoungGen: 4696K->4696K(6144K)] 17196K->17220K(19968K), 0.0017783 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Ergonomics) [PSYoungGen: 4696K->4546K(6144K)] [ParOldGen: 12524K->12496K(13824K)] 17220K->17043K(19968K), [Metaspace: 3465K->3465K(1056768K)], 0.0059779 secs] [Times: user=0.05 sys=0.00, real=0.00 secs] 
[GC (Allocation Failure) --[PSYoungGen: 4546K->4546K(6144K)] 17043K->17043K(19968K), 0.0010228 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 4546K->0K(6144K)] [ParOldGen: 12496K->640K(8704K)] 17043K->640K(14848K), [Metaspace: 3465K->3465K(1056768K)], 0.0071660 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[B@45ee12a7
-----------------------------
null
null
null
null
[B@45ee12a7

```

由输出结果可知，当准备放入第四个字节对象数组（`B@135fbaa4`）之前，发生了`Minor GC`；当准备放入第五个字节对象数组（`B@45ee12a7`）之前，发生了`Minor GC`和`Full GC`，当`Full GC`结束后，发现内存依旧不够，此时又先触发了`Minor GC`，发现内存还不够，又触发了`Full GC`，此时回收软引用对象。

最终，`B@45ee12a7`被成功添加，集合中只剩下了一个字节数组对象，前四个全在第二次`Full GC`时被回收了

## 引用队列

当一个软引用持有的对象（如`byte[]`）被回收后，软引用对象本身会被加入到一个引用队列中，此引用队列被一个叫做 `Handler Reference` 的守护线程监控着，这个线程会隔一段时间来这个队列里看看，如果有对象，就会将对象的引用清除，从而软引用对象本身也就可以被垃圾回收了。

使用引用队列，必须将引用队列与软引用对象进行关联

### 示例

```java
public class Demo {
    private final static int _4mb = 4 * 1024 * 1024;
    public static void softRef() {
        List<SoftReference<byte[]>> list = new ArrayList<>();
        // 引用队列
        ReferenceQueue<byte[]> referenceQueue = new ReferenceQueue<>();
        for (int i = 0; i < 5; i++) {
             // 关联了引用队列， 当软引用所关联的 byte[]被回收时，软引用自己会加入到 queue 中去
            SoftReference<byte[]> softReference = new SoftReference<>(new byte[_4mb], referenceQueue);
            list.add(softReference);
            System.out.println(softReference.get());
        }
		// 从队列中获取无用的 软引用对象，并移除
        Reference<? extends byte[]> ref = referenceQueue.poll();
        while (null != ref) {
            list.remove(ref);
            ref = referenceQueue.poll();
        }

        System.out.println("-----------------------------");
        for (SoftReference<byte[]> reference : list) {
            System.out.println(reference.get());
        }
    }

    public static void main(String[] args) {
        softRef();
    }
}
```

```java
[B@14ae5a5
[B@7f31245a
[B@6d6f6e28
[GC (Allocation Failure) [PSYoungGen: 2162K->488K(6144K)] 14450K->12984K(19968K), 0.0056704 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[B@135fbaa4
[GC (Allocation Failure) --[PSYoungGen: 4696K->4696K(6144K)] 17192K->17200K(19968K), 0.0132994 secs] [Times: user=0.02 sys=0.00, real=0.01 secs] 
[Full GC (Ergonomics) [PSYoungGen: 4696K->4561K(6144K)] [ParOldGen: 12504K->12481K(13824K)] 17200K->17043K(19968K), [Metaspace: 3465K->3465K(1056768K)], 0.0232222 secs] [Times: user=0.03 sys=0.00, real=0.02 secs] 
[GC (Allocation Failure) --[PSYoungGen: 4561K->4561K(6144K)] 17043K->17043K(19968K), 0.0022046 secs] [Times: user=0.02 sys=0.00, real=0.00 secs] 
[Full GC (Allocation Failure) [PSYoungGen: 4561K->0K(6144K)] [ParOldGen: 12481K->640K(8704K)] 17043K->640K(14848K), [Metaspace: 3465K->3465K(1056768K)], 0.0077182 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[B@45ee12a7
-----------------------------
[B@45ee12a7
```



当代码执行完

```java
for (int i = 0; i < 5; i++) {
    SoftReference<byte[]> softReference = new SoftReference<>(new byte[_4mb], referenceQueue);
    list.add(softReference);
    System.out.println(softReference.get());
}
```

`referenceQueue`队列中已经存在软引用度了，因为前面已经发生过垃圾回收。

遍历此队列，将`list`中的软引用对象移除

```java
Reference<? extends byte[]> ref = referenceQueue.poll();
while (null != ref) {
    list.remove(ref);
    ref = referenceQueue.poll();
}
```

最终只打印了一个对象`B@45ee12a7`

```java
for (SoftReference<byte[]> reference : list) {System.out.println(reference.get());}
```

