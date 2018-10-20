## Java集合类——Map 

#### 1. Java中的Map主要有哪几种？之间有什么区别 

- HashMap：非线程安全
- HashTable ：线程安全
- LinkedHashMap ： 非线程安全，是HashMap 的一个子类，它保留插入的顺序。 
- ConcurrentHashMap ：线程安全，锁力度比HashTable细，性能比HashTable好

#### 2. Java中遍历Map的几种方式 

1. 在for-each循环中使用entries来遍历 （常用）在遍历之前需要验证map是否为空，否则报NullPointerException 

   ```java
   Map<Integer, Integer> map = new HashMap<Integer, Integer>();  
   for (Map.Entry<Integer, Integer> entry : map.entrySet()) {  
       System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());  
   }  
   ```

2. 在for-each循环中通过keySet或values来实现遍历（只遍历key或只遍历value时用）性能比遍历entrySet要好

   ```java
   Map<Integer, Integer> map = new HashMap<Integer, Integer>();  
   //遍历map中的键   
   for (Integer key : map.keySet()) {    
       System.out.println("Key = " + key);  
   }    
   //遍历map中的值   
   for (Integer value : map.values()) {    
       System.out.println("Value = " + value);    
   }  
   ```

3. 使用Iterator遍历 优点可以在遍历时调用iterator.remove()来删除entries 

   ```java
   //使用泛型：
   Map<Integer, Integer> map = new HashMap<Integer, Integer>();   
   Iterator<Map.Entry<Integer, Integer>> entries = map.entrySet().iterator();  
   while (entries.hasNext()) {   
       Map.Entry<Integer, Integer> entry = entries.next();  
       System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());    
   }  
   //不使用泛型：
   Map map = new HashMap();    
   Iterator entries = map.entrySet().iterator();   
   while (entries.hasNext()) {    
       Map.Entry entry = (Map.Entry) entries.next();  
       Integer key = (Integer)entry.getKey();    
       Integer value = (Integer)entry.getValue();    
       System.out.println("Key = " + key + ", Value = " + value);    
   }  
   ```

4. 通过键找值遍历（效率低） 

   ```java
   Map<Integer, Integer> map = new HashMap<Integer, Integer>();  
   for (Integer key : map.keySet()) {    
       Integer value = map.get(key);    
       System.out.println("Key = " + key + ", Value = " + value);    
   }  
   ```

- 总结：
  1. 只需要key或者value用2
  2. 版本低于java5，或打算在遍历时删除entries用3
  3. 否则用1

#### 3. HashMap和HashTable有何不同？ 

|                    |           HashMap            |                       HashTable                       |
| :----------------: | :--------------------------: | :---------------------------------------------------: |
|      线程安全      |            不安全            |                         安全                          |
|        继承        | 继承AbstractMap实现了Map接口 |                    继承Dictionary                     |
|     允许为null     |        key可以为null         |                key和value都不可为null                 |
|    哈希值的使用    |        重新计算hash值        |                直接使用对象的hashCode                 |
| 遍历方式的内部实现 | 实现 Iterator，支持fast-fail | Enumeration方式不支持fast-fail  Iterator支持fast-fail |

#### 4. HashMap 和 ConcurrentHashMap 的区别（jdk1.8以前的版本 ）

- ConcurrentHashMap： 使用桶数组实现，对桶进行了分段并且每个分段上加了锁，所以线程安全
- HashMap ：使用桶数组实现，没有分段，没有锁，不是线程安全的

#### 5. HashTable和ConcurrentHashMap之间有什么区别（jdk1.8以前的版本 ）

- HashTable ：线程安全，使用synchronized保证线程安全，是同步容器
- ConcurrentHashMap：线程安全，采用分段锁保证线程安全（16个桶）锁粒度更细，是并发容器

#### 6. HashMap和TreeMap的区别是什么？ 

- HashMap 非线程安全，无序，在Java 8之前，HashMap 底层是通过Hash表实现的 
- TreeMap 非线程安全，有序 ，底层由红黑树实现的 

#### 7. ConcurrentHashMap和LinkedHashMap有什么区别 

|                  |     ConcurrentHashMap      |        LinkedHashMap         |
| :--------------: | :------------------------: | :--------------------------: |
|     线程安全     |     线程安全（分段锁）     |          非线程安全          |
|    允许为null    |       都不允许为null       |         都允许为null         |
| 是否允许重复数据 | key不可重复，value可以重复 | key重复会覆盖，value可以重复 |
|     是否有序     |            无序            |   有序（根据放入次序排列）   |

#### 8. 所有的类都可以作为Map的key吗？有什么需要注意的吗？ 

- 最好使用不可变类型，缓存hashcode提高效率
- 如果用可变类型作为key,需要重写equals方法并且要重写hashcode方法

#### 9. 什么是ConcurrentSkipListMap？他和ConcurrentHashMap有什么区别 

- ConcurrentSkipListMap是一个内部使用跳表，并且支持排序和并发的一个Map，是线程安全的。 

- 主要区别 ：

  a. 底层实现方式不同。

  ​	ConcurrentSkipListMap底层基于跳表。

  ​	ConcurrentHashMap底层基于Hash桶和红黑树。 

  b. ConcurrentHashMap不支持排序。

  ​    ConcurrentSkipListMap支持排序。 



以上内容来自hollis直面java模块