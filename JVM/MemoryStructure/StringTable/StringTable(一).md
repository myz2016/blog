# String table(一)

```java
public class A {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
    }
}
```

将以上代码进行编译，然后使用`javap -v A.class`输出反编译后的字节码。

```java
Classfile /D:/idea-work/Practice/jvm/out/A.class
  Last modified 2019-12-19; size 455 bytes
  MD5 checksum 0d71a0c8725aea25fc5e0003ee9230f8
  Compiled from "A.java"
public class A
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#24         // java/lang/Object."<init>":()V
   #2 = String             #25            // a
   #3 = String             #26            // b
   #4 = String             #27            // ab
   #5 = Class              #28            // A
   #6 = Class              #29            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               LA;
  #14 = Utf8               main
  #15 = Utf8               ([Ljava/lang/String;)V
  #16 = Utf8               args
  #17 = Utf8               [Ljava/lang/String;
  #18 = Utf8               s1
  #19 = Utf8               Ljava/lang/String;
  #20 = Utf8               s2
  #21 = Utf8               s3
  #22 = Utf8               SourceFile
  #23 = Utf8               A.java
  #24 = NameAndType        #7:#8          // "<init>":()V
  #25 = Utf8               a
  #26 = Utf8               b
  #27 = Utf8               ab
  #28 = Utf8               A
  #29 = Utf8               java/lang/Object
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
      stack=1, locals=4, args_size=1
         0: ldc           #2                  // String a
         2: astore_1
         3: ldc           #3                  // String b
         5: astore_2
         6: ldc           #4                  // String ab
         8: astore_3
         9: return
      LineNumberTable:
        line 7: 0
        line 8: 3
        line 9: 6
        line 10: 9
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      10     0  args   [Ljava/lang/String;
            3       7     1    s1   Ljava/lang/String;
            6       4     2    s2   Ljava/lang/String;
            9       1     3    s3   Ljava/lang/String;
}
SourceFile: "A.java"

```

##常量池&运行时常量池

![image-20191221153353315](StringTable(一).assets/image-20191221153353315.png)

![image-20200205135057962](StringTable(一).assets/image-20200205135057962.png)

常量池（`Constant pool`），就是一张表，虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量等信息。1.6与1.8常量池的位置是不同的，后面会继续讨论这个问题。

运行时常量池，常量池存在于 `*.class` 文件中的，当该类被加载，它的常量池信息就会放入运行时常量
池，并把里面的符号地址变为真实地址。

## 串池

String table 俗称串池，数据结构是哈希表，用于存储字符串对象

## 分析 main 方法运行过程

`P.S. 为了展示更加清晰，表示常量池的图形中并不包括全部的常量池符号，只是用到的部分`

### 第一步

`0: ldc  #2          // String a`

`#2 = String  #25     // a`

`#25 = Utf8  a`

1. 去常量池`#2`的位置
2. 去`#25`的位置找到了`a`符号（<font color='red'>注意：此时`a`只是符号，不是字符串</font>）
3. 将`a`符号变成<font color='red'>字符串对象</font>
4. 去串池中找`a`这个字符串对象，结果没找到，然后将`a`字符串对象放进串池

![](StringTable(一).assets/image-20191219104427424.png)

### 第二步

`2: astore_1`

将字符串`a`存入`LocalVariableTable（main栈帧中的局部变量表）`中`1`的位置，存入`1`号位置的字符串对象`a`与串池中的字符串对象`a`是同一个。

![image-20191219104509322](StringTable(一).assets/image-20191219104509322.png)

### 第三步

`3: ldc           #3                  // String b`

`#3 = String             #26            // b`

`#26 = Utf8               b`

1. 去常量池`#3`的位置
2. 去`#26`的位置找到了`b`符号（<font color='red'>注意：此时`b`只是符号，不是字符串</font>）
3. 将`b`符号变成<font color='red'>字符串对象</font>
4. 去串池中找`b`这个字符串对象，结果没找到，然后将`b`字符串对象放进串池

![image-20191219105218752](StringTable(一).assets/image-20191219105218752.png)

### 第四步

`5: astore_2`

将字符串`b`存入`LocalVariableTable（main栈帧中的局部变量表）`中`2`的位置，存入`2`号位置的字符串对象`b`与串池中的字符对象`b`是同一个

![image-20191219105406445](StringTable(一).assets/image-20191219105406445.png)

### 第五步

`6: ldc           #4                  // String ab`

`#4 = String             #27            // ab`

`#27 = Utf8               ab`

1. 去常量池`#4`的位置
2. 去`#27`的位置找到了`ab`符号（<font color='red'>注意：此时`ab`只是符号，不是字符串</font>）
3. 将`ab`符号变成<font color='red'>字符串对象</font>
4. 去串池中找`ab`这个字符串对象，结果没找到，然后将`ab`字符串对象放进串池

### 第六步

`8: astore_3`

将字符串`ab`存入`LocalVariableTable（main栈帧中的局部变量表）`中`3`的位置，存入`3`号位置的字符串对象`ab`与串池中的字符对象`ab`是同一个

![image-20191219105846619](StringTable(一).assets/image-20191219105846619.png)

### 第七步

`9: return`

返回，`main`方法结束

## 总结

1. 串池中的字符串对象并不是刚开始就被全部放进去的，而是用到了才会被放进去。即，代码执行到了

   `String s1 = "a"`这一行，开始创建`a`字符串对象，才会放进串池。可以认为将字符串对象放进串池的操作是一种懒惰的行为

2. 在串池中找到了所需的字符串对象，就用串池中的，如果没找到，就将新创建字符串对象放进去，总之，串池中取值不同的字符串对象只有一份

3. `ldc #x `：读取常量池`x`位置的符号

4. `astore_x`：将从常量池中取出的符号存储到`LocalVariableTable`中`x`位置

> https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.ldc
>
> https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.astore