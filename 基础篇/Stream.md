## java 8-Stream

#### 1. 创建Stream

- 创建空的Stream对象 

  ```java
  Stream stream = Stream.empty();
  ```

- Stream .of () 用数值或数组创建Stream

  ```java
  Stream<String> words = Stream.of("Java", "Magazine", "is", "the", "best");
  ```

- Stream.iterate和Stream.generate  从函数中创建Stream，必要时使用limit方法限制输出个数

  ```java
  // 从1开始组装一个序列，1,1+1,2+1,3+1······10+1
  Stream.iterate(1, n -> n <= 10,
                 n -> n+1).forEach(System.out::println);
  
  // 随机生成10个数
  Stream.generate(() -> Math.random()).limit(10).forEach(System.out::println);
  ```

  

- Stream 可以并行处理数据：
  - parallelStream() // 并行Stream
  - stream() //单行Stream

#### 2. Stream处理数据可以分为两部分

- 中间操作部分，这些操作生成的都是stream型的数据

  - filter 过滤

    ```  java
    objectList.stream() .filter(t -> {t.getType().equals(objEnum.OPEN);
     t.getNum >= 10;                                        }).collect(toList());
    ```

  - sort 排序

    多重排序 ： Comparator.comparing().thenComparing(）

    倒序 ：.reversed()

    ```java
    objectList.stream().soeted(Comparator.comparing(obj::getId)
              .thenComparing(t -> t.getScore())).reversed()
              .collect(Collectors.toList());
    ```

  - map 提取信息

    ```java
    List<Long> idList = objectList.stream()
                    .map(obj::getId)
                    .collect(toList());
    ```

    可以用mapToInt, mapToDouble, and mapToLong将通用Stream转化成一个数值型Stream 

    ```java
    int numSum = objectList.stream()
                 .mapToInt(obj::getNum).sum();
    ```

  - distinct 去重

  - limit(n) 限制几个

  - skip(n) 忽略前几个

  - flatMap 对Stream<String>  这种类型的数据取其中的String 组成Stream后进行后续操作

    ```JAVA
    /**
     * map 
     **/
    String[] cc = {"hello", "world"};
    Stream<String[]> stream = Arrays.asList(cc).stream().map(str  						-> str.split(""));
    // 对stream 进行map操作
    Stream<Stream<String>> streamStream = stream.map(s
    					  -> Arrays.stream(s));
    List<Stream<String>> streamList = 		                                           streamStream.collect(Collectors.toList());
    
    /**
     * flatMap 
     **/
    Stream<String[]> stream1 = Arrays.asList(cc).stream().map(str 						-> str.split(""));
    // 对stream1进行flatMap操作
    Stream<String> streamFlatMap = stream1.flatMap(s
    					  ->Arrays.stream(s));
    List<String> streamFlatMapList = 	      		                                  streamFlatMap.collect(Collectors.toList());
    ```

- 处理管道

  - collect

    - collect(counting())  计算数量

    - collect(summingInt(Transaction::getValue))  对各种类型的数据求和

      ​	summingDouble() summingInt(), summingLong()  

    - collect(averagingInt(Transaction::getValue))  对各种类型数据求平均值

      ​	averagingDouble(), averagingInt(), averagingLong()  

    - collect(maxBy(comparing(Transaction::getValue)))  求某个属性的最大最小值 

      ​	maxBy()和minBy() 

    - collect(groupingBy(Transaction::getCurrency)) 或collect(groupingBy(Transaction::getCurrency, toList())); 

      对数据进行分组

  - forEach 遍历

  - anyMatch 只要其中有一个元素满足传入的Predicate时返回True，否则返回False 

  - allMatch 所有元素均满足传入的Predicate时返回True，否则False 

  - noneMatch 所有元素均不满足传入的Predicate时返回True，否则False 

#### 3. 参考链接

> - [使用Java 8 Stream像操作SQL一样处理数据（上）](https://juejin.im/post/5a5eba2c518825732739a028)
> - [使用Java 8 Stream像操作SQL一样处理数据（下）](https://juejin.im/post/5a616731f265da3e5b32de36)
> - [Java8新特性学习-Stream的Reduce及Collect方法详解](https://blog.csdn.net/icarusliu/article/details/79504602)
> - [Java8新特性学习-函数式编程(Stream/Function/Optional/Consumer)](https://blog.csdn.net/icarusliu/article/details/79495534)

