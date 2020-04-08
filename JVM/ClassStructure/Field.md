

# Class Structure-Field

## 源码

```java
public class HelloJava2 {
    private int a = 4;
    private int b = 5;
    private long c = 10L;
    public static void main(String[] args) {
        System.out.println("Hello Java");
    }
}
```

## 二进制字节码（16进制）

```java
CA FE BA BE 00 00 00 34 00 2F 0A 00 0B 00 1E 09 
00 0A 00 1F 09 00 0A 00 20 05 00 00 00 00 00 00 
00 0A 09 00 0A 00 21 09 00 22 00 23 08 00 24 0A 
00 25 00 26 07 00 27 07 00 28 01 00 01 61 01 00 
01 49 01 00 01 62 01 00 01 63 01 00 01 4A 01 00 
06 3C 69 6E 69 74 3E 01 00 03 28 29 56 01 00 04 
43 6F 64 65 01 00 0F 4C 69 6E 65 4E 75 6D 62 65 
72 54 61 62 6C 65 01 00 12 4C 6F 63 61 6C 56 61 
72 69 61 62 6C 65 54 61 62 6C 65 01 00 04 74 68 
69 73 01 00 0C 4C 48 65 6C 6C 6F 4A 61 76 61 32 
3B 01 00 04 6D 61 69 6E 01 00 16 28 5B 4C 6A 61 
76 61 2F 6C 61 6E 67 2F 53 74 72 69 6E 67 3B 29 
56 01 00 04 61 72 67 73 01 00 13 5B 4C 6A 61 76 
61 2F 6C 61 6E 67 2F 53 74 72 69 6E 67 3B 01 00 
0A 53 6F 75 72 63 65 46 69 6C 65 01 00 0F 48 65 
6C 6C 6F 4A 61 76 61 32 2E 6A 61 76 61 0C 00 11 
00 12 0C 00 0C 00 0D 0C 00 0E 00 0D 0C 00 0F 00 
10 07 00 29 0C 00 2A 00 2B 01 00 0A 48 65 6C 6C 
6F 20 4A 61 76 61 07 00 2C 0C 00 2D 00 2E 01 00 
0A 48 65 6C 6C 6F 4A 61 76 61 32 01 00 10 6A 61 
76 61 2F 6C 61 6E 67 2F 4F 62 6A 65 63 74 01 00 
10 6A 61 76 61 2F 6C 61 6E 67 2F 53 79 73 74 65 
6D 01 00 03 6F 75 74 01 00 15 4C 6A 61 76 61 2F 
69 6F 2F 50 72 69 6E 74 53 74 72 65 61 6D 3B 01 
00 13 6A 61 76 61 2F 69 6F 2F 50 72 69 6E 74 53 
74 72 65 61 6D 01 00 07 70 72 69 6E 74 6C 6E 01 
00 15 28 4C 6A 61 76 61 2F 6C 61 6E 67 2F 53 74 
72 69 6E 67 3B 29 56 00 21 00 0A 00 0B 00 00 00 
03 00 02 00 0C 00 0D 00 00 00 02 00 0E 00 0D 00 
00 00 02 00 0F 00 10 00 00 00 02 00 01 00 11 00 
12 00 01 00 13 00 00 00 4C 00 03 00 01 00 00 00 
16 2A B7 00 01 2A 07 B5 00 02 2A 08 B5 00 03 2A 
14 00 04 B5 00 06 B1 00 00 00 02 00 14 00 00 00 
12 00 04 00 00 00 05 00 04 00 06 00 09 00 07 00 
0E 00 08 00 15 00 00 00 0C 00 01 00 00 00 16 00 
16 00 17 00 00 00 09 00 18 00 19 00 01 00 13 00 
00 00 37 00 02 00 01 00 00 00 09 B2 00 07 12 08 
B6 00 09 B1 00 00 00 02 00 14 00 00 00 0A 00 02 
00 00 00 0A 00 08 00 0B 00 15 00 00 00 0C 00 01 
00 00 00 09 00 1A 00 1B 00 00 00 01 00 1C 00 00 
00 02 00 1D

```

## 反编译字节码

```java
Classfile /D:/idea-work/Practice/jvm/out/HelloJava2.class
  Last modified 2019-12-31; size 644 bytes
  MD5 checksum 6cc4d1ece92472797d5b690b81de14d4
  Compiled from "HelloJava2.java"
public class HelloJava2
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #11.#30        // java/lang/Object."<init>":()V
   #2 = Fieldref           #10.#31        // HelloJava2.a:I
   #3 = Fieldref           #10.#32        // HelloJava2.b:I
   #4 = Long               10l
   #6 = Fieldref           #10.#33        // HelloJava2.c:J
   #7 = Fieldref           #34.#35        // java/lang/System.out:Ljava/io/PrintStream;
   #8 = String             #36            // Hello Java
   #9 = Methodref          #37.#38        // java/io/PrintStream.println:(Ljava/lang/String;)V
  #10 = Class              #39            // HelloJava2
  #11 = Class              #40            // java/lang/Object
  #12 = Utf8               a
  #13 = Utf8               I
  #14 = Utf8               b
  #15 = Utf8               c
  #16 = Utf8               J
  #17 = Utf8               <init>
  #18 = Utf8               ()V
  #19 = Utf8               Code
  #20 = Utf8               LineNumberTable
  #21 = Utf8               LocalVariableTable
  #22 = Utf8               this
  #23 = Utf8               LHelloJava2;
  #24 = Utf8               main
  #25 = Utf8               ([Ljava/lang/String;)V
  #26 = Utf8               args
  #27 = Utf8               [Ljava/lang/String;
  #28 = Utf8               SourceFile
  #29 = Utf8               HelloJava2.java
  #30 = NameAndType        #17:#18        // "<init>":()V
  #31 = NameAndType        #12:#13        // a:I
  #32 = NameAndType        #14:#13        // b:I
  #33 = NameAndType        #15:#16        // c:J
  #34 = Class              #41            // java/lang/System
  #35 = NameAndType        #42:#43        // out:Ljava/io/PrintStream;
  #36 = Utf8               Hello Java
  #37 = Class              #44            // java/io/PrintStream
  #38 = NameAndType        #45:#46        // println:(Ljava/lang/String;)V
  #39 = Utf8               HelloJava2
  #40 = Utf8               java/lang/Object
  #41 = Utf8               java/lang/System
  #42 = Utf8               out
  #43 = Utf8               Ljava/io/PrintStream;
  #44 = Utf8               java/io/PrintStream
  #45 = Utf8               println
  #46 = Utf8               (Ljava/lang/String;)V
{
  public HelloJava2();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: iconst_4
         6: putfield      #2                  // Field a:I
         9: aload_0
        10: iconst_5
        11: putfield      #3                  // Field b:I
        14: aload_0
        15: ldc2_w        #4                  // long 10l
        18: putfield      #6                  // Field c:J
        21: return
      LineNumberTable:
        line 5: 0
        line 6: 4
        line 7: 9
        line 8: 14
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      22     0  this   LHelloJava2;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #8                  // String Hello Java
         5: invokevirtual #9                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 10: 0
        line 11: 8
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       9     0  args   [Ljava/lang/String;
}

```

以上的源码、字节码、反编译的字节码是全部的内容，下面在分析时，剔除了不需要的部分。

ClassFile Structure（类结构）

> 4.1. The `ClassFile` Structure 
>
> https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-FieldType

```java
ClassFile {
    u4             magic;
    u2             minor_version;
    u2             major_version;
    u2             constant_pool_count;
    cp_info        constant_pool[constant_pool_count-1];
    u2             access_flags;
    u2             this_class;
    u2             super_class;
    u2             interfaces_count;
    u2             interfaces[interfaces_count];
    u2             fields_count;
    field_info     fields[fields_count];
    u2             methods_count;
    method_info    methods[methods_count];
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```

```java
72 69 6E 67 3B 29 56 00 21 00 0A 00 0B 00 00 00 
03 00 02 00 0C 00 0D 00 00 00 02 00 0E 00 0D 00 
00 00 02 00 0F 00 10 00 00 00 02 00 01 00 11 00 
12 00 01 00 13 00 00 00 4C 00 03 00 01 00 00 00 
16 2A B7 00 01 2A 07 B5 00 02 2A 08 B5 00 03 2A 
14 00 04 B5 00 06 B1 00 00 00 02 00 14 00 00 00 
12 00 04 00 00 00 05 00 04 00 06 00 09 00 07 00 
0E 00 08 00 15 00 00 00 0C 00 01 00 00 00 16 00 
16 00 17 00 00 00 09 00 18 00 19 00 01 00 13 00 
00 00 37 00 02 00 01 00 00 00 09 B2 00 07 12 08 
B6 00 09 B1 00 00 00 02 00 14 00 00 00 0A 00 02 
00 00 00 0A 00 08 00 0B 00 15 00 00 00 0C 00 01 
00 00 00 09 00 1A 00 1B 00 00 00 01 00 1C 00 00 
00 02 00 1D
```

## 分析类结构

### access_flags

对应字节码 `00 21`（21为16进制表示）

72 69 6E 67 3B 29 56<span style='color:red'> **00 21**</span> 00 0A 00 0B 00 00 00 

`access_flags`对照表

> **Table 4.1-A. Class access and property modifiers**
>
> https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-FieldType

| Flag Name        | Value  | Interpretation                                               |
| ---------------- | ------ | ------------------------------------------------------------ |
| `ACC_PUBLIC`     | 0x0001 | Declared `public`; may be accessed from outside its package. |
| `ACC_FINAL`      | 0x0010 | Declared `final`; no subclasses allowed.                     |
| `ACC_SUPER`      | 0x0020 | Treat superclass methods specially when invoked by the *invokespecial* instruction. |
| `ACC_INTERFACE`  | 0x0200 | Is an interface, not a class.                                |
| `ACC_ABSTRACT`   | 0x0400 | Declared `abstract`; must not be instantiated.               |
| `ACC_SYNTHETIC`  | 0x1000 | Declared synthetic; not present in the source code.          |
| `ACC_ANNOTATION` | 0x2000 | Declared as an annotation type.                              |
| `ACC_ENUM`       | 0x4000 | Declared as an `enum` type.                                  |

21 = 0x0001 + 0x0020，即 `ACC_PUBLIC` + `ACC_SUPER`，表示此类是一个公共类

### this_class

对应字节码`00 0A` 

72 69 6E 67 3B 29 56 00 21 <font color='red'>**00 0A** </font>00 0B 00 00 00

`0A`表示根据常量池中`10`找到本类全限定名

`#10 = Class    #39   // HelloJava2`

`#39 = Utf8  HelloJava2`

由以上代码得知，本类全限定名为`HelloJava2`

###  super_class

对应字节码`00 0B` 

72 69 6E 67 3B 29 56 00 21 00 0A <font color='red'>00 0B  </font>00 00 00

`0B`表示根据常量池中`11`找到本类全限定名

`#11 = Class    #40    // java/lang/Object`

`#40 = Utf8    java/lang/Object`

由以上代码得知，本类全限定名为`Object`

### interfaces_count

对应字节码`00 00`

72 69 6E 67 3B 29 56 00 21 00 0A 00 0B <font color='red'>**00 00**</font> 00

接口数量为`0`个

### fields_count

对应字节码`00 03`

72 69 6E 67 3B 29 56 00 21 00 0A 00 0B 00 00 <font color='red'>**00**</font>

<font color='red'>**03**</font> 00 02 00 0C 00 0D 00 00 00 02 00 0E 00 0D 00

成员变量数量为`3`个

### field_info

> 4.5. Fields 
>
> https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-FieldType

```java
field_info {
    u2             access_flags;
    u2             name_index;
    u2             descriptor_index;
    u2             attributes_count;
    attribute_info attributes[attributes_count];
}
```

**以上为`field_info`的结构，下面会根据字节码依次分析每一项**

> **Table 4.5-A. Field access and property flags** 
>
> https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-FieldType

| Flag Name       | Value  | Interpretation                                               |
| --------------- | ------ | ------------------------------------------------------------ |
| `ACC_PUBLIC`    | 0x0001 | Declared `public`; may be accessed from outside its package. |
| `ACC_PRIVATE`   | 0x0002 | Declared `private`; usable only within the defining class.   |
| `ACC_PROTECTED` | 0x0004 | Declared `protected`; may be accessed within subclasses.     |
| `ACC_STATIC`    | 0x0008 | Declared `static`.                                           |
| `ACC_FINAL`     | 0x0010 | Declared `final`; never directly assigned to after object construction (JLS §17.5). |
| `ACC_VOLATILE`  | 0x0040 | Declared `volatile`; cannot be cached.                       |
| `ACC_TRANSIENT` | 0x0080 | Declared `transient`; not written or read by a persistent object manager. |
| `ACC_SYNTHETIC` | 0x1000 | Declared synthetic; not present in the source code.          |
| `ACC_ENUM`      | 0x4000 | Declared as an element of an `enum`.                         |

通过类结构得知，`fields_count`如果有值，则下面为`field_info`

#### access_flags

对应字节码为`00 02`

03 <font color='red'>**00 02**</font> 00 0C 00 0D 00 00 00 02 00 0E 00 0D 00

02 = 0x0002，即`ACC_PRIVATE`，表示成员变量类型为私有

#### name_index

对应字节码为`00 0C`

03 00 02 <font color='red'>**00 0C**</font> 00 0D 00 00 00 02 00 0E 00 0D 00

`0C`表示根据常量池中`12`，找到成员变量的名称`a`

`#12 = Utf8   a`

由以上代码得知，成员变量的名称`a`

#### descriptor_index

对应字节码为`00 0D`

03 00 02 00 0C <font color='red'>**00 0D**</font> 00 00 00 02 00 0E 00 0D 00

`0D`表示根据常量池中`13`

`#13 = Utf8   I`

由以上代码得知，成员变量类型为`I`

`I`表示什么意思呢？参见下方表格

> **Table 4.3-A. Interpretation of field descriptors** 
>
> https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html#jvms-4.3.2

| *FieldType* term    | Type        | Interpretation                                               |
| ------------------- | ----------- | ------------------------------------------------------------ |
| `B`                 | `byte`      | signed byte                                                  |
| `C`                 | `char`      | Unicode character code point in the Basic Multilingual Plane, encoded with UTF-16 |
| `D`                 | `double`    | double-precision floating-point value                        |
| `F`                 | `float`     | single-precision floating-point value                        |
| `I`                 | `int`       | integer                                                      |
| `J`                 | `long`      | long integer                                                 |
| `L` *ClassName* `;` | `reference` | an instance of class *ClassName*                             |
| `S`                 | `short`     | signed short                                                 |
| `Z`                 | `boolean`   | `true` or `false`                                            |
| `[`                 | `reference` | one array dimension                                          |

由此得知，`I`表示类型为`int`

对应源码中`private int a = 4`

#### attributes_count

对应字节码为`00 00`

03 00 02 00 0C 00 0D <font color='red'>**00 00**</font> 00 02 00 0E 00 0D 00

`attributes_count`数量为`0`

### 其他成员变量

`private int b = 5`，字节的顺序同样为`access_flags、name_index、descriptor_index、attributes_count`，字节码如下所示

03 00 02 00 0C 00 0D 00 00 <font color='red'>**00 02 00 0E 00 0D 00**</font>

<font color='red'>**00**</font> 00 02 00 0F 00 10 00 00 00 02 00 01 00 11 00

分析方式与`a`相同，就不在详细描述了

`private long c = 10L`，字节顺序与上面的一样，不同的是成员变量的类型，在字节码中为

00 00 02 00 0F <font color='red'>**00 10**</font> 00 00 00 02 00 01 00 11 00

`10`表示根据常量池中`16`

`#16 = Utf8   J`

由`Table 4.3-A. Interpretation of field descriptors`得知，`J`对应的类型为`long`，也就是`private long c = 10L`的类型
