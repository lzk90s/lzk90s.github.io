# java

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [java](#java)
  - [==和 equals 的区别](#和-equals-的区别)
  - [包装类的常量池](#包装类的常量池)
  - [String 常量池](#string-常量池)
  - [自动拆箱装箱](#自动拆箱装箱)
  - [ClassNotFoundException 和 NoClassDefFoundException 的区别？](#classnotfoundexception-和-noclassdeffoundexception-的区别)
  - [多线程](#多线程)
    - [线程池 ThreadPoolExecutor](#线程池-threadpoolexecutor)
    - [java 中线程池种类](#java-中线程池种类)
    - [BlockingQueue](#blockingqueue)
    - [java 实现定时器的几种方法](#java-实现定时器的几种方法)
    - [java 创建线程的几种方法](#java-创建线程的几种方法)
    - [ThreadLocal](#threadlocal)
    - [synchronized 作用于静态方法和非静态方法的区别？](#synchronized-作用于静态方法和非静态方法的区别)
    - [synchronized 的实现原理](#synchronized-的实现原理)
    - [volatile 关键字的作用](#volatile-关键字的作用)
    - [jvm 中的主内存和工作内存](#jvm-中的主内存和工作内存)
  - [锁](#锁)
    - [锁的实现思路](#锁的实现思路)
    - [乐观锁和悲观锁](#乐观锁和悲观锁)
    - [乐观锁的实现：CAS（compare and swap）](#乐观锁的实现cascompare-and-swap)
    - [什么是 CAS 的自旋](#什么是-cas-的自旋)
    - [什么是 ABA 问题？](#什么是-aba-问题)
    - [ABA 问题的影响](#aba-问题的影响)
    - [如何解决 ABA 问题？](#如何解决-aba-问题)
    - [java 中的 ABA 方案：AtomicStampedReference](#java-中的-aba-方案atomicstampedreference)
    - [AtomicXXX 的原理](#atomicxxx-的原理)
    - [Lock 和 synchronized 的区别？](#lock-和-synchronized-的区别)
    - [分段锁的原理：ConcurrentHashMap](#分段锁的原理concurrenthashmap)
    - [HashMap, HashTable, ConcurrentHashMap 的区别？](#hashmap-hashtable-concurrenthashmap-的区别)
    - [AQS（AbstractQueuedSynchronizer）是什么？](#aqsabstractqueuedsynchronizer是什么)
    - [CountDownLatch 的用途和原理](#countdownlatch-的用途和原理)
    - [Semaphore 的用途和原理](#semaphore-的用途和原理)
    - [CyclicBarrier 的用途和原理](#cyclicbarrier-的用途和原理)
    - [CountDownLatch 和 CyclicBarrier 的区别？](#countdownlatch-和-cyclicbarrier-的区别)
  - [java 命令常用参数](#java-命令常用参数)
  - [jdk 中的其他命令](#jdk-中的其他命令)
    - [jstack](#jstack)
    - [jmap](#jmap)

<!-- /code_chunk_output -->

## ==和 equals 的区别

- ==比较内存地址
- equals 调用对象的 equal 方法比较。

## 包装类的常量池

- Java 基本类型的包装类的大部分都实现了常量池技术，即 Byte,Short,Integer,Long,Character,Boolean。
- Float,Double 没有实现常量池
- Java 缓存了-128 到 127 范围内的 Integer 类。所以当你给这个范围的封装类赋值时，装箱操作会调用 Integer.valueOf() 函数，反过来就会给封装类赋上一个缓存池实例的引用。
  另一方面来说，如果用超出这个范围的值给封装类赋值时，Integer.valueOf()会创建一个新的 Integer 类。因此，比对超出这个范围的 Integer 类时，会返回 false。

```java
public static Integer valueOf(int i) {
    return i >= -128 && i <= Integer.IntegerCache.high ? Integer.IntegerCache.cache[i + 128] : new Integer(i);
}
```

## String 常量池

- 直接使用双引号声明出来的 String 对象会直接存储在常量池中，例如，String str = "111"
- string 可以通过 intern()方法，把一个字符串加入常量池中，例如，String str = new String("111").intern()

## 自动拆箱装箱

- 简单来说装箱就是自动将基本数据类型转换为包装器类型，拆箱就是自动将包装器类型转化为基本类型。
- 通过反编译 class 文件可知，装箱的时候调用的是 Integer 的 valueOf 方法，拆箱时调用的是 Integer 的 intValue 方法

其他的包装器类也类似

## ClassNotFoundException 和 NoClassDefFoundException 的区别？

- jvm 的 classloader 加载类时，如果在 classpath 中没有找到 class,则是 ClassNotFoundException，通常是设置 classpath 不对，或者缺少 java 包。
- class 如果加载成功之后，会有一个该类的类对象（class 对象），当在内存当中没有找到这个类对象，就会出现 NotClassDefFoundError，通常是因为 class 中存在 static 方法，static 方法执行失败导致 class 没有加载到内存中。

## java 命令常用参数

- -Xss512K 指定 JVM 的栈大小
- Xms 指定 JVM 初始占用的堆内存大小
- -Xmx 指定 JVM 最大堆内存大小

## jdk 中的其他命令

### jstack

### jmap
