#### 5. 什么是NIO

- NIO与IO的作用和目的是相同的，都是计算机与外部世界或者一个程序与计算机的其余部分的之间的接口，只不过，IO是以流的方式处理数据，而NIO是以块的方式处理数据
- IO：流式数据创建过滤器很容易，面向流的IO慢
- NIO：按照块处理比按照流处理的快，但是缺少优雅性和简单性

#### 6. 什么是AIO

​	Java AIO即Async非阻塞，是异步非阻塞的IO。 

#### 7. 什么是BIO

​	Java BIO即Block I/O ， 同步并阻塞的IO。 

#### 8. NIO,BIO,AIO之间的区别与联系

- BIO：同步阻塞IO模式，必须等待这件事情做完了才去做下一件事情
- AIO：异步非阻塞IO模式，不用等待这件事情做完了才去做下一件事，这件事情做完了就会自动告诉我他做完了
- NIO：同时支持阻塞与非阻塞模式

#### 9. Java中BIO、NIO、AIO分别适用哪些场景

- BIO：适用于连接数较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中，JDK1.4以前的唯一选择，但程序直观简单易理解 
- NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。 
- AIO方式适用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持。 