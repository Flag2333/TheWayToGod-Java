## Redis

#### 1. 什么是Redis

- Redis是一种基于键值对的NoSQL数据库，与很多键值对数据库不同的地方在于Redis的值可以有多种数据结构和算法组成；Redis将所有数据都存放在内存里，读写性能惊人；Redis可以利用快照和日志的形式把数据保存在硬盘；Redis提供了键过期，发布订阅，事务，流水线，lua脚本等附加功能。

#### 2. 为什么用Redis

- 支持键值数据类型：字符串，散列，列表，集合，有序集合，并且可以对集合进行运算操作
- Redis所有数据都存储在内存里，读写速度快于硬盘，并且支持将内存中的数据异步写入硬盘中
- 可以用作缓存（设置失效时间），消息队列（list类型实现队列）等
- 支持多数据库（分片）
- 支持事务（multi，exec，watch，incr）

#### 3. 什么时候用Redis

- 缓存

- 单点登录

- 排行榜系统

- 计数器应用

- 社交网络

- 消息队列

- 秒杀

- 等

#### 4. 为什么Redis这么快

- Redis所有数据都存储在内存里，内存操作快于硬盘
- Redis是用C语言实现
- 对管道的支持，由于内存操作很快，时间一般都耽误在网络传输上，Redis可以一块执行很多命令后再返回结果
- 采用单线程架构，预防多线程产生的竞争问题，如果有多线程竞争锁的时间 ，好几个单线程都执行完毕了
- 采用了非阻塞I/O多路复用机制 

#### 5. redis的数据类型，以及每种数据类型的使用场景 

- ##### String ：

  - 允许存储的数据的最大容量是512M

  - 可以存储

    1. 简单字符串
    2. 复杂字符串（json，xml）
    3. 数字（整数，浮点）
    4. 二进制（图片，音频，视频）

  - 批量设置值 mset key value [key value...]

    批量获取值 mget key [key...]

  - 计数 incr key

    结果有三种情况 

    1. 值不是整数，返回错误
    2. 值是整数，返回自增后的结果
    3. 键不存在，按照值为0自增，返回结果1

  - 内部编码 ：Redis根据字符串长度，自动选择内部编码

    1. int：8个字节长整型
    2. embstr：小于等于39个字节的字符串
    3. raw：大于39个字节的字符串

  - 使用场景

    1. 缓存功能：先从Redis中取数据，如果Redis中没有则从数据库获取，并且写回到Redis中并且设置过期时间
    2. 计数
    3. 共享session：把用户登录信息存到Redis中并设置过期时间7天等
    4. 限速：Redis开启计数模式，规定在某一时间长度内可以访问多少次，如果超过限制则不予通过

- ##### hash

  - 批量设置或获取field-value

    设置：hmset key field [key field...]

    获取：hmget key field [field...]

  - 计算field个数 hlen key

  - - 获取所有field hkeys key

       1) "name"

       2) "age"

       3) "city"

    - 获取所有value hvals key

       1) "mike"

       2) "12"

       3) "天津"

    - 获取所有的field-value hgetall key

       1) "name"

       2) "mike"

       3) "age"

       4) "12"

  - 内部编码

    1. ziplist （压缩列表）使用更加紧凑的结构实现多个元素的连续存储，在节省内存方面比hashtable更加优秀，条件：
       - 当哈希类型的元素个数小于 hash-max-ziplist-entries配置（默认512个）
       - 并且所有值都小于hash-max-ziplist-value配置（默认64个）
    2. hashtable （哈希表）不能用ziplist的时候用，读写时间复杂度O(1)

  - 使用场景

    hmset user:1 name tom age 23 city beijing

    hmset user:2 name tim age 24 habit football birth 19980425

    - 与关系型数据库的区别
      1. hash类型是稀疏的，关系型是结构化的；例如hash每个键可以有不同的field，而关系型添加一列，每一行都要设置值（即使为null）
      2. 关系型数据库可以进行关系查询，Redis不能

- ##### list

  - 一个列表最多可以存储2^32 -1个元素

  - 有序

  - 可重复

  - 添加和弹出

    - 从左边

      插入 lpush key value [value...]

      弹出 lpop key

    - 从右边

      插入 rpush key value [value...]

      弹出 rpop key

    - 向某个元素前或者后插入 linsert key before|after pivot value

    - 删除指定元素 lrem key count value (找到等于value的元素进行删除)

      1. count > 0 从左到右删除最多count个元素
      2. count < 0 从右到左删除最多count绝对值个元素
      3. count = 0 删除所有

    - 修改指定索引下标的元素 lset key index newValue

    - blpop brpop 阻塞弹出

  - 内部编码

    1. ziplist （压缩列表）
       - 当列表元素个数小于 list-max-ziplist-entries配置（默认512个）
       - 并且所有值都小于 list-max-ziplist-value配置（默认64个）
    2. linkedlist（链表）不能用ziplist的时候用

  - 使用场景

    1. **消息队列**

       **lpush+brpop** 命令组合即可实现阻塞队列

       生产者客户端使用lpush从列表左侧插入元素，多个消费者客户端使用brpop命令阻塞式的抢列表尾部的元素，多个客户端保证了消费的负载均衡和高可用性

    2. **分页获取文章列表**

  - **ps**：lpush + lpop = Stack (栈)

    ​	lpush + rpop = Queue (队列)

    ​	lpush + ltrim = Capped Collection (有限集合)

    ​	lpush + brpop = Massage Queue (消息队列)

- ##### set

  - 一个集合最多可以存储2^32 -1个元素

  - 无序

  - 不可重复

  - 支持多个集合去交集，并集，差集

  - 集合内操作

    - 随机从集合返回指定个数元素（count可以不写，默认为1）

      srandmember key [count]

  - 集合间操作

    - 求多个集合的交集 sinter key [key...]

    - 求多个集合的并集 suinon key [key...]

    - 求多个集合的差集 sdiff key [key...]

    - 将交集，并集，差集的结果保存

      sinterstore destination key [key...]

      suionstore destination key [key...]

      sdiffstore  destination key [key...]

  - 内部编码

    1. intset（整数集合）
       - 当集合中的元素都是整数且元素个数小于 set-max-ziplist-entries配置（默认512个）
    2. hashtable（哈希表）不能用intset的时候用

  - 使用场景

    - 标签（tag）

  - ps：sadd = Tagging (标签)

    ​	spop/srandmember = Random item (生成随机数，抽奖)

    ​	sadd + sinter = Social Graph (社交需求)

- ##### 有序集合

  - 有序（根据score排序，score可以重复）

  - 不可重复

  - 计算成员排名

    zrank key member 从低到高

    zrevrank key member 从高到低

  - 内部编码

    1. ziplist （压缩列表）
       - 当有序集合元素个数小于 zset-max-ziplist-entries配置（默认512个）
       - 并且所有值都小于 zset-max-ziplist-value配置（默认64个）
    2. skiplist（跳跃表）不能用ziplist的时候用

  - 使用场景

    - 排行榜系统（用户获得赞，时间，播放量。。。）


#### 8、Redis的持久化机制

- RDB：当前进程数据以快照的形式保存到硬盘

  - 方式：

    - 手动触发：

      1. save命令：造成阻塞，已废弃

      2. bgsave命令：

         1）fork操作创建子进程

         2）子进程生成RDB文件

         3）子进程通知主进程“好了”

    - 自动触发：save命令的相关配置

  - 优点：

    - 生成压缩二级制文件，用于备份，是全量复制
    - 加载RDB用于恢复数据快于AOF文件

  - 缺点：

    - 不能实时持久化/秒级持久化
    - 老版本Redis不兼容新版本RDB文件

- AOF：以独立日志的方式记录每次写命令，重启是再重新执行AOF文件中的命令，已达到恢复数据的目的

  - 开启方式：appendonly yes 默认是不开启的

  - 流程：
    1. 命令写入AOF缓冲区
    2. 从缓冲区同步到硬盘AOF文件，有不同的同步策略
    3. AOF文件会越来越大，这时候会重写AOF文件以达到压缩体积的作用；把Redis进程内的数据转化为写命令，同步到新的AOF文件
    4. 当Redis服务器重启时可以加载AOF文件进行恢复，如果同时存在AOF文件和RDB文件，优先加载AOF文件，再加载RDB文件

  - 重写AOF文件

    - 为什么重写之后的AOF文件的体积会减小

      1. 进程中已经超时的不写
      2. 过程写命令不写，只写最终数据的写命令
      3. 多条命令可以合并成一个，以64个元素拆分多条

    - 触发重写的方式

      1. 手动触发 bgrewriteaof命令
      2. 自动触发 需配置

    - 重写的流程

      ![Redis持久化 AOF文件重写运作流程](https://github.com/Flag2333/TheWayToGod-Java/blob/master/img/Redis%E6%8C%81%E4%B9%85%E5%8C%96%20AOF%E6%96%87%E4%BB%B6%E9%87%8D%E5%86%99%E8%BF%90%E4%BD%9C%E6%B5%81%E7%A8%8B.jpg)

      1）执行AOF重写请求

      2）父进程执行fork创建子进程，开销等同于bgsave

      3.1）主进程fork操作完成后，继续响应其他命令。所有的修改命令依然写入AOF缓冲区并根据同步策略同步到硬盘，保证原有AOF机制正确性

      3.2）由于fork操作运用写时复制技术，子进程只能共享fork操作时的内存数据。由于父进程依然响应命令，Redis使用“AOF重写缓冲区”保存这部分新数据，防止新AOF文件生成期间丢失这部分数据

      4）子进程根据内存快照，按照命令合并规则写入到新的AOF文件。每次批量写入硬盘数据由配置控制。默认32M，防止单次刷盘数据过多造成硬盘阻塞
      5.1）新AOF文件写入完成后，子进程发送信号给父进程，父进程更新统计信息
      5.2）父进程把AOF重写缓冲区的数据写入到新的AOF文件
      5.3）使用新的AOF文件替换老文件，完成AOF重写  

#### 8. 缓存的收益和成本

- 收益：
  - 加速读写：缓存通常都是全内存的，存储层读写性能相对较弱
  - 降低后端负载：减少后端的访问量和负载（很复杂的SQL）
  - 开销大的复杂运算
  - 加速请求响应：Redis每秒可以完成数万次读写，可以批量操作
- 成本：
  - 数据不一致
  - 代码维护成本
  - 运维成本

#### 9、如何应对缓存穿透和缓存雪崩问题 

- 什么是缓存穿透：查询一个根本不存在的数据，缓存和存储层都不会命中，会使后端存储层负载加大
  - 原因：
    1. 代码问题
    2. 恶意攻击
  - 解决方式：缓存空对象
- 什么是缓存雪崩：缓存层挂了，请求全部怼到存储层
  - 解决方式：
    1. 保证缓存高可用
    2. 隔离组件，为后端限流或降级
    3. 提前演练

#### 10、如何解决redis的并发竞争问题 

- 利用redis自带的incr命令 

- 使用乐观锁的方式 Redis的watch

- 这个是针对客户端来的，在代码里要对redis操作的时候，针对同一key的资源，就先进行加锁（java里的synchronized或lock）。 

  例子：

  ```java
  	/**
       * 简单乐观锁
       */
      public boolean secKillBubble(Integer bubbleId, Integer userId) {
          Jedis jedis = getJedis();
          // 第一层保障 从0自增key1
          Long incr = jedis.incr(RedisConstant.bubbleEntryNum + bubbleId);
          // 给key1设置过期时间
          jedis.expire(RedisConstant.bubbleEntryNum+ bubbleId, RedisConstant.bubble_exp);
          try {
              // 监视key2
              jedis.watch(RedisConstant.bubbleForOne + bubbleId);// watchkeys
              // 如果key1没有被自增过则开启事务
              if (incr <=1) {
                  Transaction tx = jedis.multi();// 开启事务
                  // 设置key2的值为用户id(抢到气泡的用户的id)
                  tx.setex(RedisConstant.bubbleForOne + bubbleId,RedisConstant.bubble_exp, userId.toString());
                  // 第二个人过来发现这个气泡已经被设置了用户id 说明已经被别人秒杀掉了 
                  List<Object> list = tx.exec();// 提交事务，如果此时watchkeys被改动了，则返回null
                  if (list != null) {
                      log.info("气泡秒杀成功!" + ",userId" + userId + "，气泡Id：" + bubbleId);
                      return true;
                  }
              }
              log.info("气泡没有秒杀到" + ",userId" + userId + "，气泡Id：" + bubbleId);
              return false;
          } catch (Exception e) {
              log.error("错误：秒杀气泡" + ",userId" + userId + "，气泡Id：" + bubbleId, e);
              e.printStackTrace();
          } finally {
              jedis.close();
          }
          return false;
      }
  ```

  

#### 11、如何使用Redis实现分布式锁

- 什么是分布式锁

#### 11. Redis是单线程的，为什么要这么设计

#### 14、Redis和Memcacaed有什么区别

- 数据结构方面：Redis和Memcacaed都是K-V结构，但是Redis支持更多数据类型
- 持久化方面：Redis支持持久化，缓存挂掉后可以恢复，Memcacaed不支持，数据丢失无法恢复
- 线程方面：Redis是单线程，Memcacaed多线程
- 使用底层模型不同

#### 16. 事务与lua

#### 17. 发布订阅

#### 18. 慢查询分析

#### 12. Redis读写分离

#### 13. Redis主从复制

#### 8、redis和数据库双写一致性问题

#### 7、redis的过期策略以及内存淘汰机制

#### 19. 什么是“二八定律” 

​	80%的业务访问集中在20%的数据上。这时为了减轻数据的压力和提高网站的数据访问速度，则可以使用缓存机制来优化网站。 

#### 20. 什么是“热数据和冷数据” 

- 热数据：访问频率较高的数据
- 冷数据：访问频率较低的数据

#### 21. 什么是缓存预热、缓存更新、缓存降级 

- [阿里P8技术专家细究分布式缓存问题](https://www.toutiao.com/a6533812974807679495/?tt_from=mobile_qq&utm_campaign=client_share&timestamp=1521327601&app=news_article&utm_source=mobile_qq&iid=7163591049&utm_medium=toutiao_ios)

- 缓存预热

  > 缓存预热这个应该是一个比较常见的概念，相信很多小伙伴都应该可以很容易的理解，缓存预热就是系统上线后，将相关的缓存数据直接加载到缓存系统。这样就可以避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题！用户直接查询事先被预热的缓存数据！
  >
  > 针对上面的技术我特意整理了一下，如果想学习Java工程化、高性能及分布式、深入浅出。微服务、Spring，MyBatis，Netty源码分析的朋友可以加Java进阶群：582505643，群里有阿里大牛直播讲解技术，以及Java大型互联网开源技术的视频免费分享给大家。
  >
  > 解决思路：
  >
  > 1、直接写个缓存刷新页面，上线时手工操作下；
  >
  > 2、数据量不大，可以在项目启动的时候自动进行加载；
  >
  > 3、定时刷新缓存；

- 缓存更新

  > 除了缓存服务器自带的缓存失效策略之外（Redis默认的有6中策略可供选择），我们还可以根据具体的业务需求进行自定义的缓存淘汰，常见的策略有两种：
  >
  > （1）定时去清理过期的缓存；
  >
  > （2）当有用户请求过来时，再判断这个请求所用到的缓存是否过期，过期的话就去底层系统得到新数据并更新缓存。
  >
  > 两者各有优劣，第一种的缺点是维护大量缓存的key是比较麻烦的，第二种的缺点就是每次用户请求过来都要判断缓存失效，逻辑相对比较复杂！具体用哪种方案，大家可以根据自己的应用场景来权衡。

- 缓存降级

  > 当访问量剧增、服务出现问题（如响应时间慢或不响应）或非核心服务影响到核心流程的性能时，仍然需要保证服务还是可用的，即使是有损服务。系统可以根据一些关键数据进行自动降级，也可以配置开关实现人工降级。
  >
  > 降级的最终目的是保证核心服务可用，即使是有损的。而且有些服务是无法降级的（如加入购物车、结算）。
  >
  > 在进行降级之前要对系统进行梳理，看看系统是不是可以丢卒保帅；从而梳理出哪些必须誓死保护，哪些可降级；比如可以参考日志级别设置预案：
  >
  > （1）一般：比如有些服务偶尔因为网络抖动或者服务正在上线而超时，可以自动降级；
  >
  > （2）警告：有些服务在一段时间内成功率有波动（如在95~100%之间），可以自动降级或人工降级，并发送告警；
  >
  > （3）错误：比如可用率低于90%，或者数据库连接池被打爆了，或者访问量突然猛增到系统能承受的最大阀值，此时可以根据情况自动降级或者人工降级；
  >
  > （4）严重错误：比如因为特殊原因数据错误了，此时需要紧急人工降级。

#### 22. Redis 性能的瓶颈是啥

- 机器内存大小，影响Redis存储的数据量

- 网络带宽，改善方式管道pipeline


