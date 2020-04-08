# String table (二)

```java
public class A {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2;
    }
}
```

```java
Classfile /D:/idea-work/Practice/jvm/out/A.class
  Last modified 2019-12-19; size 639 bytes
  MD5 checksum 564d24a86404d3e94b94b11fa113fc49
  Compiled from "A.java"
public class A
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #10.#29        // java/lang/Object."<init>":()V
   #2 = String             #30            // a
   #3 = String             #31            // b
   #4 = String             #32            // ab
   #5 = Class              #33            // java/lang/StringBuilder
   #6 = Methodref          #5.#29         // java/lang/StringBuilder."<init>":()V
   #7 = Methodref          #5.#34         // java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
   #8 = Methodref          #5.#35         // java/lang/StringBuilder.toString:()Ljava/lang/String;
   #9 = Class              #36            // A
  #10 = Class              #37            // java/lang/Object
  #11 = Utf8               <init>
  #12 = Utf8               ()V
  #13 = Utf8               Code
  #14 = Utf8               LineNumberTable
  #15 = Utf8               LocalVariableTable
  #16 = Utf8               this
  #17 = Utf8               LA;
  #18 = Utf8               main
  #19 = Utf8               ([Ljava/lang/String;)V
  #20 = Utf8               args
  #21 = Utf8               [Ljava/lang/String;
  #22 = Utf8               s1
  #23 = Utf8               Ljava/lang/String;
  #24 = Utf8               s2
  #25 = Utf8               s3
  #26 = Utf8               s4
  #27 = Utf8               SourceFile
  #28 = Utf8               A.java
  #29 = NameAndType        #11:#12        // "<init>":()V
  #30 = Utf8               a
  #31 = Utf8               b
  #32 = Utf8               ab
  #33 = Utf8               java/lang/StringBuilder
  #34 = NameAndType        #38:#39        // append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
  #35 = NameAndType        #40:#41        // toString:()Ljava/lang/String;
  #36 = Utf8               A
  #37 = Utf8               java/lang/Object
  #38 = Utf8               append
  #39 = Utf8               (Ljava/lang/String;)Ljava/lang/StringBuilder;
  #40 = Utf8               toString
  #41 = Utf8               ()Ljava/lang/String;
{
  public A();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 5: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   LA;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=5, args_size=1
         0: ldc           #2                  // String a
         2: astore_1
         3: ldc           #3                  // String b
         5: astore_2
         6: ldc           #4                  // String ab
         8: astore_3
         9: new           #5                  // class java/lang/StringBuilder
        12: dup
        13: invokespecial #6                  // Method java/lang/StringBuilder."<init>":()V
        16: aload_1
        17: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        20: aload_2
        21: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        24: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
        27: astore        4
        29: return
      LineNumberTable:
        line 7: 0
        line 8: 3
        line 9: 6
        line 10: 9
        line 11: 29
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      30     0  args   [Ljava/lang/String;
            3      27     1    s1   Ljava/lang/String;
            6      24     2    s2   Ljava/lang/String;
            9      21     3    s3   Ljava/lang/String;
           29       1     4    s4   Ljava/lang/String;
}
```

有了上一篇文章的知识积累，这里分析`String s4 = s1 + s2;`这一行代码的执行过程。

`9: new  #5  // class java/lang/StringBuilder`

创建（new）了一个`StringBuilder`对象（实际上是去常量池找`#5`的位置，然后继续往下查找，由于在上一篇中对此过程已做过描述，在此不再描述在常量池中寻址的过程）

`13: invokespecial #6   // Method java/lang/StringBuilder."<init>":()V`

调用`StringBuilder`中的一个特殊方法`init()`，其实就是构造方法。`()`括号中没有任何东西，表示调用的是无参构造。

`16: aload_1`

调用方法之前要先把参数准备好，所以此指令表示从`LocalVariableTable`中`1`的位置拿到（与`astore_1`相反）对应的字符串对象，即`s1`。

`17: invokevirtual #7                  // Method java/lang/StringBuilder.append`

调用`append`方法，并将`s1`作为`append`方法的参数。

`20: aload_2
 21: invokevirtual #7  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;`

拿到`LocalVariableTable`中`2`号位置对应的字符串对象，并将它作为参数传递给`append`方法

`24: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;`

调用`StringBuilder`的`toString`方法

`27: astore        4`

将`toStirng`的结果存入`LocalVariableTable`中`4`号位置

`StringBuilder#toString`源码

```java
@Override
    public String toString() {
        // Create a copy, don't share the array
        return new String(value, 0, count);
    }
```

由`StringBuilder`的`toString`方法源码可见，根据当前拼接好的值又创建了一个新的值为`ab`的字符串对象。由于此字符串对象使用`new`关键字创建，所以此对象被分配到了堆上。

`LocalVariableTable`中`4`号位置存的是对象的引用。

## 基于以上的知识积累，回答问题。

### 问题一

```java
public class A {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2;
        System.out.println(s3 == s4);
    }
}
```

`System.out.println(s3 == s4)`这句话的结果是什么？

分析：`s3`的`ab`是在串池中的字符串对象，`s4`引用的是一个新的字符串对象，虽然两个的值是一样的，但是存储的位置（`s3`：串池，`s4`：堆）是不一样的，是两个对象。`==`比较的是地址是否相等，所以结果是`false`

### 问题二

```java
public class A {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2;
        String s5 = "a" + "b";
        System.out.println(s3 == s5);
    }
}
```

`String s5 = "a" + "b";`

`s5`是将`a`、`b`两个常量使用加号拼接

`String s4 = s1 + s2;`

`s4`是将`s1`、`s2`两个变量拼接

那么这两种拼接会有什么不一样呢？下面来看字节码。

```java
Classfile /D:/idea-work/Practice/jvm/out/A.class
  Last modified 2019-12-19; size 906 bytes
  MD5 checksum 85101702974073f48032386fbdcc02ff
  Compiled from "A.java"
public class A
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #12.#36        // java/lang/Object."<init>":()V
   #2 = String             #37            // a
   #3 = String             #38            // b
   #4 = String             #39            // ab
   #5 = Class              #40            // java/lang/StringBuilder
   #6 = Methodref          #5.#36         // java/lang/StringBuilder."<init>":()V
   #7 = Methodref          #5.#41         // java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
   #8 = Methodref          #5.#42         // java/lang/StringBuilder.toString:()Ljava/lang/String;
   #9 = Fieldref           #43.#44        // java/lang/System.out:Ljava/io/PrintStream;
  #10 = Methodref          #45.#46        // java/io/PrintStream.println:(Z)V
  #11 = Class              #47            // A
  #12 = Class              #48            // java/lang/Object
  #13 = Utf8               <init>
  #14 = Utf8               ()V
  #15 = Utf8               Code
  #16 = Utf8               LineNumberTable
  #17 = Utf8               LocalVariableTable
  #18 = Utf8               this
  #19 = Utf8               LA;
  #20 = Utf8               main
  #21 = Utf8               ([Ljava/lang/String;)V
  #22 = Utf8               args
  #23 = Utf8               [Ljava/lang/String;
  #24 = Utf8               s1
  #25 = Utf8               Ljava/lang/String;
  #26 = Utf8               s2
  #27 = Utf8               s3
  #28 = Utf8               s4
  #29 = Utf8               s5
  #30 = Utf8               StackMapTable
  #31 = Class              #23            // "[Ljava/lang/String;"
  #32 = Class              #49            // java/lang/String
  #33 = Class              #50            // java/io/PrintStream
  #34 = Utf8               SourceFile
  #35 = Utf8               A.java
  #36 = NameAndType        #13:#14        // "<init>":()V
  #37 = Utf8               a
  #38 = Utf8               b
  #39 = Utf8               ab
  #40 = Utf8               java/lang/StringBuilder
  #41 = NameAndType        #51:#52        // append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
  #42 = NameAndType        #53:#54        // toString:()Ljava/lang/String;
  #43 = Class              #55            // java/lang/System
  #44 = NameAndType        #56:#57        // out:Ljava/io/PrintStream;
  #45 = Class              #50            // java/io/PrintStream
  #46 = NameAndType        #58:#59        // println:(Z)V
  #47 = Utf8               A
  #48 = Utf8               java/lang/Object
  #49 = Utf8               java/lang/String
  #50 = Utf8               java/io/PrintStream
  #51 = Utf8               append
  #52 = Utf8               (Ljava/lang/String;)Ljava/lang/StringBuilder;
  #53 = Utf8               toString
  #54 = Utf8               ()Ljava/lang/String;
  #55 = Utf8               java/lang/System
  #56 = Utf8               out
  #57 = Utf8               Ljava/io/PrintStream;
  #58 = Utf8               println
  #59 = Utf8               (Z)V
{
  public A();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 5: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   LA;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=3, locals=6, args_size=1
         0: ldc           #2                  // String a
         2: astore_1
         3: ldc           #3                  // String b
         5: astore_2
         6: ldc           #4                  // String ab
         8: astore_3
         9: new           #5                  // class java/lang/StringBuilder
        12: dup
        13: invokespecial #6                  // Method java/lang/StringBuilder."<init>":()V
        16: aload_1
        17: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        20: aload_2
        21: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
        24: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
        27: astore        4
        29: ldc           #4                  // String ab
        31: astore        5
        33: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
        36: aload_3
        37: aload         5
        39: if_acmpne     46
        42: iconst_1
        43: goto          47
        46: iconst_0
        47: invokevirtual #10                 // Method java/io/PrintStream.println:(Z)V
        50: return
      LineNumberTable:
        line 7: 0
        line 8: 3
        line 9: 6
        line 10: 9
        line 11: 29
        line 12: 33
        line 13: 50
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      51     0  args   [Ljava/lang/String;
            3      48     1    s1   Ljava/lang/String;
            6      45     2    s2   Ljava/lang/String;
            9      42     3    s3   Ljava/lang/String;
           29      22     4    s4   Ljava/lang/String;
           33      18     5    s5   Ljava/lang/String;
      StackMapTable: number_of_entries = 2
        frame_type = 255 /* full_frame */
          offset_delta = 46
          locals = [ class "[Ljava/lang/String;", class java/lang/String, class java/lang/String, class java/lang/String, class java/lang/String, class java/lang/String ]
          stack = [ class java/io/PrintStream ]
        frame_type = 255 /* full_frame */
          offset_delta = 0
          locals = [ class "[Ljava/lang/String;", class java/lang/String, class java/lang/String, class java/lang/String, class java/lang/String, class java/lang/String ]
          stack = [ class java/io/PrintStream, int ]
}

```

代码执行到`String s5 = "a" + "b";`对应的字节码为

`29: ldc           #4                  // String ab`

加载常量池`4`号位置的值`ab`，它并不是先找`a`再找`b`，然后使用加号拼接，而是直接找已经拼接好的`ab`，并把它存储到`LocalVariableTable`中`5`号位置。

`6: ldc           #4                  // String ab`

`6`与`29`都是加载常量池`4`号位置的值`ab`，不同的是`6`加载并将`ab`变成字符串对象后，去串池中寻找，结果没有找到，就将`ab`字符串对象放进串池；`29`加载`ab`并变为字符串对象后，去串池中寻找，结果找到了`ab`字符串，所以就不往串池中放了，而是直接使用串池中的`ab`字符串对象。所以`LocalVariableTable`中``3``号位置与`5`号位置的字符串对象，都是串池中的，所以地址是相等的。`System.out.println(s3 == s5);`结果是`true`

## 同样都是使用加号拼接，为什么字节码执行过程不同

* `String s5 = "a" + "b";`底层做法，实际上是在编译期（`javac`）间的一个优化。`jvm`认为，`a`和`b`都是常量，都不会改变了，所以这两个常量做拼接，结果是一定的，那么在编译期间就能知道结果，肯定为`ab`。
* `String s4 = s1 + s2;`，`s1`和`s2`是变量，既然是变量，那么在运行期引用的值就有可能修改，既然有可能修改，就不能在编译期间确定，所以必须在运行期间用`StringBuilder`的方法动态拼接。