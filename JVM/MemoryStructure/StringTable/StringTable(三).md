# 字符串的懒加载

在`String table(一)`中提到过字符串对象懒加载问题，即代码执行到了才会创建字符串对象，下面证明此结论

```java
public class A {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        String s4 = s1 + s2;
        String s5 = "a" + "b";
    }
}
```

## 示例

当前代码执行位置：`String s1 = "a";`

内存情况：![image-20191219152303182](StringTable(三).assets/image-20191219152303182.png)

字符串数量：`2360`

---

当前代码执行位置：``String s2 = "b";``

内存情况：![image-20191219155758160](StringTable(三).assets/image-20191219155758160.png)

字符串数量：`2361`

多出的一个字符串对象：`a`

---

当前代码执行位置：`String s3 = "ab";`

内存情况：![image-20191219160028519](StringTable(三).assets/image-20191219160028519.png)

字符串数量：`2362`

多出的一个字符串对象：`b`

---

当前代码执行位置：`String s4 = s1 + s2;`

内存情况：![image-20191219160200159](StringTable(三).assets/image-20191219160200159.png)

字符串数量：`2363`

多出的一个字符串对象：`ab`

---

当前代码执行位置：`String s5 = "a" + "b";`

内存情况：![image-20191219160353508](StringTable(三).assets/image-20191219160353508.png)

字符串数量：`2364`

多出的一个字符串对象：`ab`

此时存在值都为`ab`的两个不同的对象：![](StringTable(三).assets/image-20191219160832631.png)

一个是`String s3 = "ab";`执行后产生的`ab`，一个是`String s4 = s1 + s2;`执行后产生的ab；一个在串池，一个在堆

---

当前代码执行位置：执行过了`String s5 = "a" + "b";`

内存的情况：![image-20191219161158157](StringTable(三).assets/image-20191219161158157.png)

字符串数量：`2364`

字符串数量并没有增加，也证明了`String s5 = "a" + "b";`并没有生成新的字符串对象，而是用的串池中的字符串对象

## 结论

并不是一下子将所有字符串放入串池，而是代码执行过了以后，才会创建字符串对象，如果串池中没有才会放入串池，如果存在就不放了,而是获取串池中已有的对象。