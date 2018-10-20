## java常用命令

#### 1. javac

#### 2. javap

#### 3. jps

- 功能

  jps(Java Virtual Machine Process Status Tool)是JDK 1.5提供的一个显示当前所有java进程pid的命令 

- 操作

  - 帮助

  ![jps -h](C:\Users\Administrator\Desktop\jps -h.png)

  - 

![jps -q](C:\Users\Administrator\Desktop\jps -q.png)

#### 3. jstack

```java
C:\Users\Administrator>jps
752 class1
4084 Jps
8936 Launcher

C:\Users\Administrator>jstack 752
2018-07-14 13:14:08
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.171-b11 mixed mode):

"main" #1 prio=5 os_prio=0 tid=0x0000000005405000 nid=0x1a9c runnable [0x0000000004e3f000]

```

```java
C:\Users\Administrator>jps
7236 class1
9428 Jps
6940 Launcher

C:\Users\Administrator>jstack 7236
2018-07-14 13:19:43
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.171-b11 mixed mode):

"Reference Handler" #2 daemon prio=10 os_prio=2 tid=0x00000000172d9800 nid=0x95c in Object.wait() [0x000000001855f000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x00000000c8e04638> (a java.lang.ref.Reference$Lock)
        at java.lang.Object.wait(Object.java:502)
        at java.lang.ref.Reference.tryHandlePending(Reference.java:191)
        - locked <0x00000000c8e04638> (a java.lang.ref.Reference$Lock)
        at
```

```java
C:\Users\Administrator>jps
8544 JStackDemo
1444 Jps
5112 Launcher

C:\Users\Administrator>jstack 8544
2018-07-14 13:33:35
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.171-b11 mixed mode):

Found one Java-level deadlock:
=============================
"Thread-1":
  waiting to lock monitor 0x000000000579b2d8 (object 0x00000000edd39a38, a java.lang.Object),
  which is held by "Thread-0"
"Thread-0":
  waiting to lock monitor 0x000000000579dd78 (object 0x00000000edd39a48, a java.lang.Object),
  which is held by "Thread-1"

Java stack information for the threads listed above:
===================================================
"Thread-1":
        at package1.DeadLockclass.run(JStackDemo.java:38)
        - waiting to lock <0x00000000edd39a38> (a java.lang.Object)
        - locked <0x00000000edd39a48> (a java.lang.Object)
        at java.lang.Thread.run(Thread.java:748)
"Thread-0":
        at package1.DeadLockclass.run(JStackDemo.java:25)
        - waiting to lock <0x00000000edd39a48> (a java.lang.Object)
        - locked <0x00000000edd39a38> (a java.lang.Object)
        at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.
```

