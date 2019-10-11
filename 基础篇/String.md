## String

#### 1. String类能不能被继承，为什么？这种设计有什么好处？

- String是final类型，final类不能被继承。 
- 好处：
  1. 缓存Hashcode ，Java中经常会用到字符串的哈希码（hashcode），保证hashcode永远一致，不用每次都重新计算一次，提高效率
  2. 使其他类的使用更加便利 
  3. 安全，不可变对象天生就是线程安全的 因为不可变对象不能被改变，所以他们可以自由地在多个线程之间共享。不需要任何同步处理。 

####  2. String s = new String("hollis"); 定义了几个对象。

- JVM为了提高性能，字符串类维护了一个字符串池，如果字符串已经存在池中，就返回池中的实例引用。如果字符串不在池中，就会实例化一个字符串并放到池中。Java能够进行这样的优化是因为字符串是不可变的，可以不用担心数据冲突进行共享。
- 常量池中没有“hollis”字面量则创建两个对象，否则创建一个对象，以及创建一个引用。 

#### 3. substring()方法到底做了什么？不同版本的JDK中是否有区别？为什么？

[链接：](http://www.hollischuang.com/archives/1232)[三张图彻底了解JDK 6和JDK 7中substring的原理及区别-HollisChuang's ...](http://www.hollischuang.com/archives/1232) 

#### 4. replaceFirst,replaceAll,replace的区别

- replaceFirst：替换第一个相同的字符串
- replace: 的参数是char和CharSequence,即可以支持字符的替换,也支持字符串的替换 ，相同的全部替换
- replaceAll :使用正则的方式匹配字符串，注意一些特殊符号需要进行转义

#### 5. String的“+”是如何实现的？

1. String s = "a" + "b"，编译器会进行常量折叠(因为两个都是编译期常量，编译期可知)，即变成 String s = "ab" 
2. 对于能够进行优化的(String s = "a" + 变量 等)用 StringBuilder 的 append() 方法替代，最后调用 toString() 方法 (底层就是一个 new String()) 

#### 6. 字符串拼接的几种方式和区别

```java
String s1 = "Hollis";
String s2 = "Chuang";
String s3 = s1 + s2;
String s4 = "Hollis" + "Chuang";

反编译后：
String s1 = "Hollis";
String s2 = "Chuang";
String s3 = (new StringBuilder()).append(s1).append(s2).toString();
String s4 = "HollisChuang";
```

- String s3 = s1 + s2，底层就是用`StringBuilder.append`实现的，字符串常量池中并没有拼接结果 HollisChuang

- 只有 String s4 = "Hollis" + "Chuang" 这句存在的情况下，会直接拼出结果，字符串常量池中会只有 HollisChuang 而没有 Hollis和Chuang

- 原因：常量池要保存的是**已确定**的字面量值。

  对于字符串的拼接，纯字面量和字面量的拼接，会把拼接结果作为常量保存到字符串池。 

  有一个参数是非字面量，是一个变量的话，整个拼接操作会被编译成`StringBuilder.append`，这种情况编译器是无法知道其确定值的。只有在运行期才能确定 ，所以不能在编译器加入常量池

  如果有很多相同的只有在运行期才能确定的字符串，可以使用intern 使其加入常量池，减少字符串的重复创建

#### 7. String.valueOf和Integer.toString的区别

```java
1.int i = 5;
2.String i1 = "" + i;
3.String i2 = String.valueOf(i);
4.String i3 = Integer.toString(i);
```

1、第三行和第四行没有任何区别，因为`String.valueOf(i)`也是调用`Integer.toString(i)`来实现的。

 2、第二行代码其实是

`String i1 = (new StringBuilder()).append(i).toString();`，

首先创建一个StringBuilder对象，然后再调用append方法，再调用toString方法 

#### 8. switch对String的支持

#### 9. 字符串池

#### 10. 常量池

- class常量池
- 运行时常量池

#### 11. 如何比较两个字符串？

- 对于字符串的比较，一般目的是比较字符串内容是否相等，这种情况下，要使用equals()方法来比较，而不是使用'=='。 
- equals()比较的是内容是否相同，'=='比较的是地址是否相同。 

#### 12. String有没有长度限制，为什么？如果有，超过限制会发生什么？

- 编译期：在Java中，所有需要保存在常量池中的数据，长度最大不能超过65535，这当然也包括字符串的定义咯。 
- String在运行期有没有限制呢，答案是有的，就是我们前文提到的那个Integer.MAX_VALUE ，这个值约等于4G，在运行期，如果String的长度超过这个范围，就可能会抛出异常。(在jdk 1.9之前） 

#### 13. String，StringBuilder和StingBuffer之间的区别与联系。

- String是字符串常量，StringBuffer是字符串变量（线程安全），StringBuilder是字符串变量（非线程安全）。
- 经常改变内容的字符串最好不用String，而如果是StringBuffer,每次结果都会对StringBuffer对象本身进行操作，而不是新的对象，所以一般情况下推荐使用StringBuffer.  
- StringBuffer上主要操作是append和insert方法，可重载这些方法以接受任意类型的数据。 
- StringBuffer兼容的API，但不保证同步，该类被设计用于StringBuffer的一个简易替换，用于字符串缓冲区被单个线程使用的时候。如果可能，建议优先使用。 大多数实现中，它比StringBuffer要快。 

#### 14. 如何理解String的intern方法，不同版本JDK有何不同？为什么？ 31期

- intern()是String类中的一个public方法，用法如下: String s1 = new String("Hollis"); String s2=s2.intern(); System.out.println( s1==s2 ); 输出结果为 true。 当一个String实例str调用intern()方法时，Java查找常量池中是否有相同Unicode的字符串常量，如果有，则返回其的引用，如果没有，则在常量池中增加一个Unicode等于str的字符串并返回它的引用；

-  不同版本的JDK中有何不同？ 先看下以下代码：

  ​	String str1 = new StringBuilder("Hollis").append("Chuang").toString(); 	

  ​	System.out.println(str1.intern() == str1); 

  打印结果： jdk6 下false jdk7 下true

- Java 6 和Java7 中intern的表现有所不同，导致不同的原因是因为在Java 7中常量池的位置从PermGen区改到了Java堆区中。 

- jdk1.6中 intern 方法会把首次遇到的字符串实例复制到永久待（常量池）中，并返回此引用；但在jdk1.7中，只是会把首次遇到的字符串实例的引用添加到常量池中（没有复制），并返回此引用。 

- 对于以上代码中的str1.intern() ，在jdk1.6中，会把“HollisChuang”这个字符串复制到常量池中，并返回他的引用。所以str2.intern()的值和str2的值，即两个对象的地址是不一样的。 对于以上代码中的str1.intern() ，在jdk1.7中，会把str2的引用保存到常量池中，并把这个引用返回。所以str2.intern()的值和str2的值是相等的。

-  扩展另外一个例子：

  ​	String str2 = new StringBuilder("ja").append("va").toString(); 	

  ​	System.out.println(str2.intern() == str2); 

  以上代码，无论在1.6还是1.7中都会返回false。

  原因是"ja" + "va" = "java"，这个常量在常量池中已经有了，因为这个字符串在Java中随处可见，曾经被初始化过。

  所以，在1.7中str2.intern()返回的内容是很久之前初始化的时候的那个引用，自然和刚刚创建的字符串的应用不相等。 