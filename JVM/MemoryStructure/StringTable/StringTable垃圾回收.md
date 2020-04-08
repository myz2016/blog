# String table 垃圾回收

## 字符串常量也会被垃圾回收

通过一个案例演示 String table 也是会被垃圾回收的

### 示例（一）

首先介绍这次案例中要用到的虚拟机参数

* -Xmx10m：设置堆内存大小为10m
* -XX:+PrintStringTableStatistics：显示串池中字符串实例的个数，占用空间的大小等
* -XX:+PrintGCDetails -verbose:gc：打印垃圾回收情况

```java
public class Demo {
    public static void main(String[] args) {
        int i = 0;
        try {

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println(i);
        }
    }
}
```



```java
Heap
 PSYoungGen      total 2560K, used 2046K [0x00000000ffd00000, 0x0000000100000000, 0x0000000100000000)
  eden space 2048K, 99% used [0x00000000ffd00000,0x00000000ffeff860,0x00000000fff00000)
  from space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
  to   space 512K, 0% used [0x00000000fff00000,0x00000000fff00000,0x00000000fff80000)
 ParOldGen       total 7168K, used 0K [0x00000000ff600000, 0x00000000ffd00000, 0x00000000ffd00000)
  object space 7168K, 0% used [0x00000000ff600000,0x00000000ff600000,0x00000000ffd00000)
 Metaspace       used 3473K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 381K, capacity 388K, committed 512K, reserved 1048576K
SymbolTable statistics:
Number of buckets       :     20011 =    160088 bytes, avg   8.000
Number of entries       :     14157 =    339768 bytes, avg  24.000
Number of literals      :     14157 =    603144 bytes, avg  42.604
Total footprint         :           =   1103000 bytes
Average bucket size     :     0.707
Variance of bucket size :     0.711
Std. dev. of bucket size:     0.843
Maximum bucket size     :         6
StringTable statistics:
Number of buckets       :     60013 =    480104 bytes, avg   8.000
Number of entries       :      1778 =     42672 bytes, avg  24.000
Number of literals      :      1778 =    158696 bytes, avg  89.255
Total footprint         :           =    681472 bytes
Average bucket size     :     0.030
Variance of bucket size :     0.030
Std. dev. of bucket size:     0.172
Maximum bucket size     :         2
```

以上是运行代码后打印的信息。现在没有发生垃圾回收，因为代码中什么事情也没有做。先来简单认识下输出的信息。

```java
Heap
 PSYoungGen      total 2560K, used 2046K [0x00000000ffd00000, 0x0000000100000000, 0x0000000100000000)
  eden space 2048K, 99% used [0x00000000ffd00000,0x00000000ffeff860,0x00000000fff00000)
  from space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
  to   space 512K, 0% used [0x00000000fff00000,0x00000000fff00000,0x00000000fff80000)
 ParOldGen       total 7168K, used 0K [0x00000000ff600000, 0x00000000ffd00000, 0x00000000ffd00000)
  object space 7168K, 0% used [0x00000000ff600000,0x00000000ff600000,0x00000000ffd00000)
 Metaspace       used 3473K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 381K, capacity 388K, committed 512K, reserved 1048576K
```

由于加入了`-XX:+PrintGCDetails`参数，所以打印出了以上内存使用的信息。

```java
StringTable statistics:
Number of buckets       :     60013 =    480104 bytes, avg   8.000
Number of entries       :      1778 =     42672 bytes, avg  24.000
Number of literals      :      1778 =    158696 bytes, avg  89.255
Total footprint         :           =    681472 bytes
Average bucket size     :     0.030
Variance of bucket size :     0.030
Std. dev. of bucket size:     0.172
Maximum bucket size     :         2
```

以上是串池的统计信息。

StringTable（串池）的底层结构的实现类似于`HashTable`，由数组和链表构成，数组的个数称之为桶（Buckets）。

`Number of buckets：60013：桶的数量为`60013

`Number of entries:1178`：键值对的个数为1178

`Number of literals:1778`：串池中字符串的个数为1178

程序什么都没有做，串池中的字符串个数已经有了1178，为什么呢？因为程序运行时，类名，方法名等这些数据也是以字符串常量表示的，它们也存在于串池中。

### 示例（二）

在代码中加入100个字符串，让其加载到串池中

```java
public class Demo {
    public static void main(String[] args) {
        int i = 0;
        try {
            for (int j = 0; j < 100; j++) {
                String.valueOf(j).intern();
                i++;
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println(i);
        }
    }
}
```

```java
Heap
 PSYoungGen      total 2560K, used 2047K [0x00000000ffd00000, 0x0000000100000000, 0x0000000100000000)
  eden space 2048K, 99% used [0x00000000ffd00000,0x00000000ffeffc08,0x00000000fff00000)
  from space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
  to   space 512K, 0% used [0x00000000fff00000,0x00000000fff00000,0x00000000fff80000)
 ParOldGen       total 7168K, used 0K [0x00000000ff600000, 0x00000000ffd00000, 0x00000000ffd00000)
  object space 7168K, 0% used [0x00000000ff600000,0x00000000ff600000,0x00000000ffd00000)
 Metaspace       used 3471K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 381K, capacity 388K, committed 512K, reserved 1048576K
SymbolTable statistics:
Number of buckets       :     20011 =    160088 bytes, avg   8.000
Number of entries       :     14159 =    339816 bytes, avg  24.000
Number of literals      :     14159 =    603192 bytes, avg  42.601
Total footprint         :           =   1103096 bytes
Average bucket size     :     0.708
Variance of bucket size :     0.711
Std. dev. of bucket size:     0.843
Maximum bucket size     :         6
StringTable statistics:
Number of buckets       :     60013 =    480104 bytes, avg   8.000
Number of entries       :      1878 =     45072 bytes, avg  24.000
Number of literals      :      1878 =    163496 bytes, avg  87.059
Total footprint         :           =    688672 bytes
Average bucket size     :     0.031
Variance of bucket size :     0.031
Std. dev. of bucket size:     0.177
Maximum bucket size     :         2
```

`Number of literals : 1878`可以明显的看到，串池中的字符串数量增加了100个。

但这100个不足以导致GC，现在将数量加到一万个，这样的话10m的堆内存就有点盛不下了，内存一不够用，就会发生垃圾回收，垃圾回收发生在内存紧张的时候。

```java
public class Demo {
    public static void main(String[] args) {
        int i = 0;
        try {
            for (int j = 0; j < 10 * 1000; j++) {
                String.valueOf(j).intern();
                i++;
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println(i);
        }
    }
}
```

```java
[GC (Allocation Failure) [PSYoungGen: 2048K->488K(2560K)] 2048K->740K(9728K), 0.0016810 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
10000
Heap
 PSYoungGen      total 2560K, used 926K [0x00000000ffd00000, 0x0000000100000000, 0x0000000100000000)
  eden space 2048K, 21% used [0x00000000ffd00000,0x00000000ffd6db78,0x00000000fff00000)
  from space 512K, 95% used [0x00000000fff00000,0x00000000fff7a020,0x00000000fff80000)
  to   space 512K, 0% used [0x00000000fff80000,0x00000000fff80000,0x0000000100000000)
 ParOldGen       total 7168K, used 252K [0x00000000ff600000, 0x00000000ffd00000, 0x00000000ffd00000)
  object space 7168K, 3% used [0x00000000ff600000,0x00000000ff63f010,0x00000000ffd00000)
 Metaspace       used 3475K, capacity 4496K, committed 4864K, reserved 1056768K
  class space    used 381K, capacity 388K, committed 512K, reserved 1048576K
SymbolTable statistics:
Number of buckets       :     20011 =    160088 bytes, avg   8.000
Number of entries       :     14159 =    339816 bytes, avg  24.000
Number of literals      :     14159 =    603192 bytes, avg  42.601
Total footprint         :           =   1103096 bytes
Average bucket size     :     0.708
Variance of bucket size :     0.711
Std. dev. of bucket size:     0.843
Maximum bucket size     :         6
StringTable statistics:
Number of buckets       :     60013 =    480104 bytes, avg   8.000
Number of entries       :     10262 =    246288 bytes, avg  24.000
Number of literals      :     10262 =    566008 bytes, avg  55.156
Total footprint         :           =   1292400 bytes
Average bucket size     :     0.171
Variance of bucket size :     0.188
Std. dev. of bucket size:     0.433
Maximum bucket size     :         3
```

`Number of literals :10262`正常情况下，应该会有`1778 + 10000 = 11778`个字符串，但是现在只有10262个，这说明有有些字符串常量被回收了。

`[GC (Allocation Failure) [PSYoungGen: 2048K->488K(2560K)] 2048K->740K(9728K), 0.0016810 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]`

通过以上信息得知，确实发生了垃圾回收

`GC (Allocation Failure)`：在内存分配失败的情况下发生了垃圾回收

`Times: user=0.00 sys=0.00, real=0.00 secs`：垃圾回收耗费的时间（由于是新生代的垃圾回收，所以速度很快）

字符串在没有任何引用的情况下，就被垃圾回收了。

以上代码中，并没有将字符串放置在集合中，所以就被垃圾回收了。

## 结论

`String table`会发生垃圾回收