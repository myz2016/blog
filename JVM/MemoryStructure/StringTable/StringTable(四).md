# String table(四)

```java
public class A {
    public static void main(String[] args) {
        String s = new String("a") + new String("b");
    }
}
```

基于前三节的知识积累，来分析这段代码。

`String s = new String("a") + new String("b");`

1. `a`字符串对象是常量，被放在了串池之中
2. `b`字符串对象是常量，被放在了串池之中
3. `new`的`String`创建在了堆中，值为`a`，<font color='red'>它与串池中的`a`值是相等的，但对象不是同一个</font>
4. `new`的`String`创建在了堆中，值为`b`，<font color='red'>它与串池中的`b`值是相等的，但它是一个新的对象，与串池中的`b`不是同一个对象</font>
5. `s`引用了一个新的对象，值为`ab`，此对象是由`StringBuilder`对象拼接创建的。<font color='red'>注意，这个`ab`仅仅存在于堆中，并没有存在于串池中</font>，因为它是动态拼接创建的，串池中目前只存了常量字符串值

执行`String s = new String("a") + new String("b");`之前

![image-20191223134733768](StringTable(四).assets/image-20191223134733768.png)

执行`String s = new String("a") + new String("b");`之后

![image-20191223134816211](StringTable(四).assets/image-20191223134816211.png)

可见多出了五个字符串对象

<table>
    <tr>
        <th>序号</th>
        <th>名称</th>
        <th>值</th>
        <th>位置</th>
    </tr>
    <tr>
		<td>1</td>        
    	<td></td>
        <td>a</td>
        <td>串池</td>
    </tr>
    <tr>
        <td>2</td>
    	<td></td>
        <td>b</td>
        <td>串池</td>
    </tr>
    <tr>
        <td>3</td>
    	<td></td>
        <td>a</td>
        <td>堆</td>
    </tr>
    <tr>
        <td>4</td>
    	<td></td>
        <td>b</td>
        <td>堆</td>
    </tr>
    <tr>
        <td>5</td>
    	<td>s</td>
        <td>ab</td>
        <td>堆</td>
    </tr>
</table>



## intern 方法

### jdk1.8

如何将存在于`ab`的字符串放进串池呢？要用到`intern()`方法。

`String s2 = s.intern()`

将这个字符串对象尝试放入串池中，如果有则并不会放入；如果没有，那么就将`s`引用的字符串对象放入串池，并返回串池中的对象。

#### 示例（一）

```java
public class A {
    public static void main(String[] args) {
        String s = new String("a") + new String("b");
        String s2 = s.intern();
        System.out.println(s2 == "ab");
        System.out.println(s == "ab");
    }
}
```

当代码执行过`System.out.println(s2 == "ab");`时，由于前面已经调用了`s.intern()`方法，所以串池中已经存在了`ab`对象，那么`==`号右边的`ab`，它在创建时，肯定是先从串池中寻找，结果找到了，所以就不会再创建新的对象，所以就用了串池中的`ab`字符串对象，也就是上一步放入串池的`ab`字符串对象。所以与`s2`是同一个对象，所以结果是`true`。

当代码执行过`System.out.println(s == "ab");`时，由于前面调用`s.intern()`方法时，尝试将`s`引用的字符串对象放入串池，当时串池中并不存在`ab`对象，所以值为`ab`的字符串对象就被放进了串池，那么`s`就会引用串池中的字符串对象，所以`s`引用的字符串对象与串池中的字符串对象是同一个，所以结果是`true`。

#### 示例（二）

```java
public class A {
    public static void main(String[] args) {
        String x = "ab";
        String s = new String("a") + new String("b");
        String s2 = s.intern();
        System.out.println(s2 == x);
        System.out.println(s == x);
    }
}
```

`main`方法开始执行，首先执行了`String x = "ab"`，串池中创建了`ab`字符串对象。当执行过`String s2 = s.intern()`时，由于串池中已经存在了`ab`字符串对象，所以`s`引用的字符串对象并没有放入串池中，并返回了串池中的`ab`字符串对象，所以`s2`引用的确是串池中的`ab`字符串对象，而`s`指向的是存在于堆上的`ab`

根据以上的分析，`System.out.println(s2 == "ab");`结果为`true`；`System.out.println(s == "ab");`结果为`false`

以上关于`intern`方法的讨论，是基于`jdk1.8`，如果是`jdk1.6`则有些不同。

### jdk1.6

`String s2 = s.intern()`

将这个字符串对象尝试放入串池中，如果有则并不会放入，如果没有，那么就将`s`引用的字符串对象<font color='red'>复制一份</font>放入串池，并返回串池中的对象，<font color='red'>放入串池的对象与`s`引用的对象是两个不同的对象</font>

#### 示例（一）

```java
public class A {
    public static void main(String[] args) {
        String x = "ab";
        String s = new String("a") + new String("b");
        String s2 = s.intern();
        System.out.println(s2 == "ab");
        System.out.println(s == "ab");
    }
}
```

以上代码的执行结果，与上面1.8版本的执行结果是一样的，不在赘述

#### 示例（二）

```java
public class A {
    public static void main(String[] args) {
        String s = new String("a") + new String("b");
        String s2 = s.intern();
        String x = "ab";
        System.out.println(s2 == x);
        System.out.println(s == x);
    }
}
```

执行`String s2 = s.intern();`这行代码，尝试将堆上的`ab`字符串对象放入串池，此时串池中不存在值为`ab`的字符串，所以复制了一份`s`的字符串对象（这是与1.8的差别），放入串池并返回，此时`s2`是串池中的字符串对象，<font color='red'>而`s`还是存在于堆上的字符串对象</font>。执行`String x = "ab";`代码，尝试将字符串对象`ab`放入串池时，发现串池中已经有了，所以就用的串池中的字符串对象。

基于以上的分析，`System.out.println(s2 == x);`这行代码的执行结果为`true`；`System.out.println(s == x);`这行代码的执行结果为`false`

## String table 特性

基于之前的学习，总结出串池具有以下特性

* 常量池中的字符串仅是符号，第一次用到时才变为对象
* 利用串池的机制，来避免重复创建字符串对象
* 字符串变量拼接的原理是 `StringBuilder` （1.8）
* 字符串常量拼接的原理是编译期优化
* 可以使用`intern`方法，主动将串池中还没有的字符串对象放入串池
  * 1.8 将这个字符串对象尝试放入串池，如果有则并不会放入，如果没有则放入串池， 会把串
    池中的对象返回
  * 1.6 将这个字符串对象尝试放入串池，如果有则并不会放入，如果没有会把此对象复制一份，
    放入串池， 会把串池中的对象返回