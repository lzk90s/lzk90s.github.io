<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [1. 整体结构](#1-整体结构)
- [2. Atomic](#2-atomic)
  - [2.1. synchronized 作用于静态方法和非静态方法的区别？](#21-synchronized-作用于静态方法和非静态方法的区别)
  - [2.2. synchronized 的实现原理](#22-synchronized-的实现原理)
  - [2.3. volatile 关键字的作用](#23-volatile-关键字的作用)
  - [2.4. volatile 底层是如何实现禁止指令重排的？](#24-volatile-底层是如何实现禁止指令重排的)
  - [2.5. volatile 为什么不能实现原子性？](#25-volatile-为什么不能实现原子性)
  - [2.6. 锁的实现思路](#26-锁的实现思路)
  - [2.7. 乐观锁和悲观锁](#27-乐观锁和悲观锁)
  - [2.8. 乐观锁的实现：CAS（compare and swap）](#28-乐观锁的实现cascompare-and-swap)
  - [2.9. 什么是 CAS 的自旋](#29-什么是-cas-的自旋)
  - [2.10. 什么是 ABA 问题？](#210-什么是-aba-问题)
  - [2.11. ABA 问题的影响](#211-aba-问题的影响)
  - [2.12. 如何解决 ABA 问题？](#212-如何解决-aba-问题)
  - [2.13. java 中的 ABA 方案：AtomicStampedReference](#213-java-中的-aba-方案atomicstampedreference)
  - [2.14. AtomicXXX 的原理](#214-atomicxxx-的原理)
  - [2.15. LongAdder（累加器） 的原理，与 AtomicLong 的区别？](#215-longadder累加器-的原理与-atomiclong-的区别)
- [3. Lock](#3-lock)
  - [3.1. AQS（AbstractQueuedSynchronizer）是什么？](#31-aqsabstractqueuedsynchronizer是什么)
  - [3.2. AQS 的原理](#32-aqs-的原理)
  - [3.3. Lock 和 synchronized 的区别？](#33-lock-和-synchronized-的区别)
- [4. 公平锁/非公平锁](#4-公平锁非公平锁)
- [5. 同步辅助类](#5-同步辅助类)
  - [5.1. CountDownLatch 的用途](#51-countdownlatch-的用途)
  - [5.2. Semaphore 的用途](#52-semaphore-的用途)
  - [5.3. CyclicBarrier 的用途](#53-cyclicbarrier-的用途)
  - [5.4. CountDownLatch 和 CyclicBarrier 的区别？](#54-countdownlatch-和-cyclicbarrier-的区别)
- [6. 并发集合](#6-并发集合)
  - [6.1. ConcurrentHashMap](#61-concurrenthashmap)
    - [6.1.1. jdk1.7 ConcurrentHashMap 如何保证线程安全？](#611-jdk17-concurrenthashmap-如何保证线程安全)
    - [6.1.2. jdk1.8 ConcurrentHashMap 如何保证线程安全？](#612-jdk18-concurrenthashmap-如何保证线程安全)
    - [6.1.3. jdk1.8 ConcurrentHashMap 扩容方式](#613-jdk18-concurrenthashmap-扩容方式)
    - [6.1.4. jdk1.8 ConcurrentHashMap 中的几个内部类](#614-jdk18-concurrenthashmap-中的几个内部类)
    - [6.1.5. jdk1.8 ConcurrentHashMap put 流程](#615-jdk18-concurrenthashmap-put-流程)
    - [6.1.6. jdk1.8 ConcurrentHashMap get 流程](#616-jdk18-concurrenthashmap-get-流程)
    - [6.1.7. jdk1.8 ConcurrentHashMap size 流程](#617-jdk18-concurrenthashmap-size-流程)
    - [6.1.8. HashMap, HashTable, ConcurrentHashMap 的区别？](#618-hashmap-hashtable-concurrenthashmap-的区别)
  - [6.2. CopyOnWriteList](#62-copyonwritelist)
    - [6.2.1. CopyOnWriteList 的 get 操作](#621-copyonwritelist-的-get-操作)
- [7. 线程池](#7-线程池)
  - [7.1. 线程池 ThreadPoolExecutor](#71-线程池-threadpoolexecutor)
  - [7.2. 线程池满了丢弃策略有哪些？](#72-线程池满了丢弃策略有哪些)
  - [7.3. java 中线程池种类](#73-java-中线程池种类)
  - [7.4. BlockingQueue](#74-blockingqueue)
  - [7.5. java 实现定时器的几种方法](#75-java-实现定时器的几种方法)
  - [7.6. java 创建线程的几种方法](#76-java-创建线程的几种方法)
  - [7.7. ThreadLocal 的实现原理](#77-threadlocal-的实现原理)
  - [7.8. ThreadLocal 如果不 remove 会怎样？](#78-threadlocal-如果不-remove-会怎样)

<!-- /code_chunk_output -->

## 1. 整体结构

- 原子 Value（atomic）
- LOCK 框架（locks）：Lock，Condition，ReadWriteLock 等
- 线程池部分：Executor，ExecutorService，Executors 工具类
- 并发集合部分：ConcurrentMap，BlockingQueue 等～
- 同步辅助类：CountDownLatch，CyclicBarrier，Semaphore 等

## 2. Atomic

### 2.1. synchronized 作用于静态方法和非静态方法的区别？

当关键字 Synchronized 加在了静态方法上，由于加静态方法获取的是类锁，那么不论基于哪个类 new 了几个实例，他只有一个 Class 对象，所以只有一把锁，多线程访问会发生互斥。

### 2.2. synchronized 的实现原理

1. 每个对象有一个监视器锁（monitor），当 monitor 被占用时就会处于锁定状态，线程执行 monitorenter 指令时尝试获取 monitor 的所有权，执行完成后会释放 monitor 的所有权
2. 添加了 synchronized 的代码块在汇编中会生成 monitorenter 和 monitorexit 指令，普通方法会生成 **ACC_SYNCHRONIZED** 标志，静态方法会多一个 **ACC_STATIC** 标志
3. 当一条线程执行时遇到 monitorenter 指令时，它会尝试去获得锁，如果获得锁，那么所计数器+1（为什么要加 1，因为它是可重入锁，可根据这个琐计数器判断锁状态），如果没有获得锁，那么阻塞，当它遇到一个 monitoerexit 时，琐计数器会-1，当计数器为 0 时，就释放锁。
4. wait/notify 等方法也依赖于 monitor 对象，这就是为什么只有在同步的块或者方法才能调用 wait/notify 等方法，否则会抛出 java.lang.IllegalMonitorStateException 异常的原因

### 2.3. volatile 关键字的作用

- 保证了不同线程对这个进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
- 防止指令重排序

> 注意：volatile 无法保证对变量的操作的原子性，所以在多线程场景下，多个线程同时 i++会出现线程不安全的问题，因为 i++不是原子操作

### 2.4. volatile 底层是如何实现禁止指令重排的？

内存屏障

### 2.5. volatile 为什么不能实现原子性？

i++这样的操作，在底层是有多条指令的，volatile 只是保证了变量更新后，用内存屏障让其他线程可见

### 2.6. 锁的实现思路

- 锁是一个共享资源
- 单机多线程情况，内存相同，可以用内存实现锁，例如 CAS，mutex 等
- 多机分布式，可以用一个统一的第三方，来实现共享锁，例如数据库，通过 update 某一行的成功与否，来判断是否获取到锁。

### 2.7. 乐观锁和悲观锁

- 悲观锁具有强烈的排他性，获取到锁后，其他人只能等待锁的释放，例如 synchronized
- 乐观锁并未真正加锁，通常是使用 CAS 方式

### 2.8. 乐观锁的实现：CAS（compare and swap）

CAS 是依赖于 cpu 的原子指令的，linux 下使用了 cmpxchg 原子性操作方法。

### 2.9. 什么是 CAS 的自旋

获取锁失败后，为了避免直接让线程进入阻塞状态而采取的循环一定次数去试着获取锁的行为，为什么能这样做呢，因为用户态和内核态的切换非常消耗系统资源，锁的持有时间一般非常短，所以一般多次尝试就能竞争到锁。

### 2.10. 什么是 ABA 问题？

改了又还原回来，感觉是没有修改过，其实已经变化了。某些情况下是致命的错误，例如链表操作。

### 2.11. ABA 问题的影响

- 操作链表过程中， CAS 问题可能会导致链表错误

### 2.12. 如何解决 ABA 问题？

ABA 问题的根本在于 cas 在修改变量的时候，无法记录变量的状态，比如修改的次数，否修改过这个变量。这样就很容易在一个线程将 A 修改成 B 时，另一个线程又会把 B 修改成 A,造成 casd 多次执行的问题。
所以，解决 ABA 问题的方法就是，引入一个业务无关的变量，来记录对业务变量的修改，通常是版本号，或者时间戳。

### 2.13. java 中的 ABA 方案：AtomicStampedReference

### 2.14. AtomicXXX 的原理

1. 使用 volatile 保存变量，保证多线程变量的可见性，使缓存失效。
2. 用 CAS+自旋方式，实现原子操作

### 2.15. LongAdder（累加器） 的原理，与 AtomicLong 的区别？

LongAdder 类与 AtomicLong 类的区别在于高并发时前者将对单一变量的 CAS 操作分散为对数组 cells 中多个元素的 CAS 操作，取值时进行求和；而在并发较低时仅对 base 变量进行 CAS 操作，与 AtomicLong 类原理相同

## 3. Lock

### 3.1. AQS（AbstractQueuedSynchronizer）是什么？

- 抽象同步队列，AQS 定义了一套多线程访问共享资源的同步器框架，ReentrantLock/Semaphore/CountDownLatch 都有使用 AQS
- 核心数据结构 双向链表+state（锁状态） 底层操作 CAS

### 3.2. AQS 的原理

1. AQS 内部使用双向链表，用于保存等待的线程。
2. 当线程竞争锁失败，会把线程构造成一个 Node,加入到双向链表中，同时阻塞该线程。
3. 当获取到锁的线程 unlock 锁后，会从队列中唤醒一个阻塞节点执行。

### 3.3. Lock 和 synchronized 的区别？

- synchronized 是关键字，Lock 是 jdk 中的接口
- synchronized 在发生异常时会自动释放占有的锁，因此不会出现死锁，而 lock 发生异常时候不会主动释放占有的锁必须调用 unlock 方法可能引发死锁。
- lock 可以通过 trylock 来知道有没有获取锁，而 synchronized 不能
- 在 synchronized 中线程 1 获取到锁后线程 2 会一直等待下去，而 lock 可以不用一直等下去。
- synchronized 的锁可重入、不可中断、非公平，而 Lock 锁可重入，可判断，可中断，可公平。
- synchronized 是一个悲观锁，Lock 是一个乐观锁（底层基于 volatile 和 cas 实现）

## 4. 公平锁/非公平锁

- 公平锁: 公平锁指多个线程按照申请顺序来依次获取锁
- 非公平锁: 非公平锁指多个线程获取锁的顺序并不是按照顺序来获取的，可以中途抢占

## 5. 同步辅助类

### 5.1. CountDownLatch 的用途

- 计数器，某个线程等待 N 个线程执行完毕后再继续执行，N 就是对应的计数，每个执行完毕就减一，直到所有完成。可以看作是 1 个等多个。
- 例如游戏中，需要多个游戏玩家都准备好了，才开始游戏，就可以使用 CountDownLatch。

### 5.2. Semaphore 的用途

- Semaphore 可以定义一个数量用以确定同时有几个任务可以同时访问，Semaphore 通常用于限制可以访问某些资源（物理或逻辑的）的线程数目。
- 例如限制流量大小，最多只能同时有几个人访问。

### 5.3. CyclicBarrier 的用途

- 让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。
- 这个屏障之所以用循环修饰，是因为在所有的线程释放彼此之后，这个屏障是可以重新使用的（reset()方法重置屏障点）。这一点与 CountDownLatch 不同

### 5.4. CountDownLatch 和 CyclicBarrier 的区别？

- 对于 CountDownLatch 来说，重点是“一个线程（多个线程）等待”，而其他的 N 个线程在完成“某件事情”之后，可以终止，也可以等待。而对于 CyclicBarrier，重点是多个线程，在任意一个线程没有完成，所有的线程都必须互相等待，然后继续一起执行。
- CountDownLatch 是计数器，线程完成一个记录一个，只不过计数不是递增而是递减，
- CyclicBarrier 更像是一个阀门，需要所有线程都到达，阀门才能打开，然后继续执行。

## 6. 并发集合

### 6.1. ConcurrentHashMap

#### 6.1.1. jdk1.7 ConcurrentHashMap 如何保证线程安全？

- 分段锁的思想是把大锁拆分成小锁，把数据分段。
- ConcurrentHashMap 使用 Segment（分段锁）技术，将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问，能够实现真正的并发访问。所以说，ConcurrentHashMap 在并发情况下，不仅保证了线程安全，而且提高了性能。

#### 6.1.2. jdk1.8 ConcurrentHashMap 如何保证线程安全？

- jdk1.8 中，采用了 synchronized+CAS 方式来保证线程安全。锁的粒度更小。
- JDK1.8 的实现降低锁的粒度，JDK1.7 版本锁的粒度是基于 Segment 的，包含多个 HashEntry，而 JDK1.8 锁的粒度就是 HashEntry（首节点）

#### 6.1.3. jdk1.8 ConcurrentHashMap 扩容方式

- 多线程方式扩容，通过给每个线程分配桶区间，避免线程间的争用，每个线程默认处理 16 个桶。
- 通过为每个桶节点加锁，避免 putVal 方法导致数据不一致。
- 同时，在扩容的时候，也会将链表拆成两份，这点和 HashMap 的 resize 方法类似。
- 如果有新的线程想 put 数据时，也会帮助其扩容。

#### 6.1.4. jdk1.8 ConcurrentHashMap 中的几个内部类

- Node：Entry
- TreeNode：红黑树节点
- TreeBin：红黑树
- ForwardingNode：扩容节点

#### 6.1.5. jdk1.8 ConcurrentHashMap put 流程

1. 如果没有初始化就先调用 initTable（）方法来进行初始化过程
2. 如果没有 hash 冲突就直接 CAS 插入
3. 如果还在进行扩容操作就先进行扩容
4. 如果存在 hash 冲突，就加锁来保证线程安全，这里有两种情况，一种是链表形式就直接遍历到尾端插入，一种是红黑树就按照红黑树结构插入，
5. 最后一个如果该链表的数量大于阈值 8，就要先转换成黑红树的结构，break 再一次进入循环(阿里面试官问题，默认的链表大小，超过了这个值就会转换为红黑树);
6. 如果添加成功就调用 addCount（）方法统计 size，并且检查是否需要扩容

#### 6.1.6. jdk1.8 ConcurrentHashMap get 流程

1. 计算 hash 值，定位到该 table 索引位置，如果是首节点符合就返回
2. 如果遇到扩容的时候，会调用标志正在扩容节点 ForwardingNode 的 find 方法，查找该节点，匹配就返回
3. 以上都不符合的话，就往下遍历节点，匹配就返回，否则最后就返回 null

#### 6.1.7. jdk1.8 ConcurrentHashMap size 流程

1. put 的时候，会调用 addCount，在添加的时候计算好了 size
2. size 使用了 LongAdder 的方式，使用 Cell 来减少锁的冲突。

#### 6.1.8. HashMap, HashTable, ConcurrentHashMap 的区别？

- HashMap 是线程不安全的，在多线程环境下，使用 HashMap 进行 put 操作时，可能会引起死循环，导致 CPU 利用率接近 100%，所以在并发情况下不能使用 HashMap。
- 不允许 key 和 value 为 null，HashTable 是线程安全的，不过比较暴力，直接 get/put 等所有相关操作都是 synchronized 的，同步代价大。
- ConcurrentHashMap 采用了“分段锁”机制代替了 HashTable 的“独占锁”，从而大大提高了性能。

### 6.2. CopyOnWriteList

#### 6.2.1. CopyOnWriteList 的 get 操作

## 7. 线程池

### 7.1. 线程池 ThreadPoolExecutor

```java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
/**
1、corePoolSize：核心线程数
    * 核心线程会一直存活，及时没有任务需要执行
    * 当线程数小于核心线程数时，即使有线程空闲，线程池也会优先创建新线程处理
    * 设置allowCoreThreadTimeout=true（默认false）时，核心线程会超时关闭

2、queueCapacity：任务队列容量（阻塞队列）
    * 当核心线程数达到最大时，新任务会放在队列中排队等待执行

3、maxPoolSize：最大线程数
    * 当线程数>=corePoolSize，且任务队列已满时。线程池会创建新线程来处理任务
    * 当线程数=maxPoolSize，且任务队列已满时，线程池会拒绝处理任务而抛出异常

4、 keepAliveTime：线程空闲时间
    * 当线程空闲时间达到keepAliveTime时，线程会退出，直到线程数量=corePoolSize
    * 如果allowCoreThreadTimeout=true，则会直到线程数量=0

5、allowCoreThreadTimeout：允许核心线程超时
6、rejectedExecutionHandler：任务拒绝处理器
    * 两种情况会拒绝处理任务：
        - 当线程数已经达到maxPoolSize，切队列已满，会拒绝新任务
        - 当线程池被调用shutdown()后，会等待线程池里的任务执行完毕，再shutdown。如果在调用shutdown()和线程池真正shutdown之间提交任务，会拒绝新任务
    * 线程池会调用rejectedExecutionHandler来处理这个任务。如果没有设置默认是AbortPolicy，会抛出异常
    * ThreadPoolExecutor类有几个内部实现类来处理这类情况：
        - AbortPolicy 丢弃任务，抛运行时异常
        - CallerRunsPolicy 执行任务
        - DiscardPolicy 忽视，什么都不会发生
        - DiscardOldestPolicy 从队列中踢出最先进入队列（最后一个执行）的任务
    * 实现RejectedExecutionHandler接口，可自定义处理器
*/

```

线程池按以下行为执行任务

1. 当线程数小于核心线程数时，创建线程。
2. 当线程数大于等于核心线程数，且任务队列未满时，将任务放入任务队列。
3. 当线程数大于等于核心线程数，且任务队列已满
   - 若线程数小于最大线程数，创建线程
   - 若线程数等于最大线程数，抛出异常，拒绝任务

### 7.2. 线程池满了丢弃策略有哪些？

1. 终止 AbortPolicy
2. 直接丢掉 DiscardPolicy
3. 把最早的丢掉 DiscardOldestPolicy
4. 继续执行 CallerRunsPolicy

### 7.3. java 中线程池种类

1. FixedThreadPool

```java
public static ExecutorService newFixedThreadPool(int nThreads){
    return new ThreadPoolExecutor(nThreads,nThreads,0L,TimeUnit.MILLISECONDS,new LinkedBlockingQueue<Runnable>());
}
/*
- 它是一种固定大小的线程池；
- corePoolSize 和 maximunPoolSize 都为用户设定的线程数量 nThreads;
- keepAliveTime 为 0，意味着一旦有多余的空闲线程，就会被立即停止掉；但这里 keepAliveTime 无效；
- 阻塞队列采用了 LinkedBlockingQueue，它是一个无界队列；由于阻塞队列是一个无界队列，因此永远不可能拒绝任务；
- 由于采用了无界队列，实际线程数量将永远维持在 nThreads，因此 maximumPoolSize 和 keepAliveTime 将无效。
*/
```

2. CachedThreadPool

```java
public static ExecutorService newCachedThreadPool(){
    return new ThreadPoolExecutor(0,Integer.MAX_VALUE,60L,TimeUnit.MILLISECONDS,new SynchronousQueue<Runnable>());
}

/**
- 它是一个可以无限扩大的线程池；
- 它比较适合处理执行时间比较小的任务；
- corePoolSize 为 0，maximumPoolSize 为无限大，意味着线程数量可以无限大;
- keepAliveTime 为 60S，意味着线程空闲时间超过 60S 就会被杀死；
- 采用 SynchronousQueue 装等待的任务，这个阻塞队列没有存储空间，这意味着只要有请求到来，就必须要找到一条工作线程处理他，如果当前没有空闲的线程，那么就会再创建一条新的线程。
*/
```

3. SingleThreadExecutor

```java
public static ExecutorService newSingleThreadExecutor(){
    return new ThreadPoolExecutor(1,1,0L,TimeUnit.MILLISECONDS,new LinkedBlockingQueue<Runnable>());
}

/**
- 它只会创建一条工作线程处理任务；
- 采用的阻塞队列为LinkedBlockingQueue；
*/

```

4. ScheduledThreadPool

```java
/**
- 它用来处理延时任务或定时任务。
- 它采用 DelayQueue 存储等待的任务 DelayQueue 内部封装了一个 PriorityQueue，它会根据 time 的先后时间排序，若 time 相同则根据 sequenceNumber 排序；
- DelayQueue 也是一个无界队列；
- 工作线程的执行过程：工作线程会从 DelayQueue 取已经到期的任务去执行；执行结束后重新设置任务的到期时间，再次放回 DelayQueue
*/
```

### 7.4. BlockingQueue

- ArrayBlockingQueue：有界缓存等待队列，数组实现，可以指定缓存队列的大小
- LinkedBlockingQueue：无界缓存等待队列。如果不指定大小，maximumPoolSizes 就相当于无效了。
- SynchronousQueue： 没有容量，是无缓冲等待队列，是一个不存储元素的阻塞队列，会直接将任务交给消费者，必须等队列中的添加元素被消费后才能继续添加新的元素
- DelayedWorkQueue： 延迟队列，ScheduledThreadPoolExecutor 使用。

### 7.5. java 实现定时器的几种方法

- Timer
- ScheduledThreadPool

### 7.6. java 创建线程的几种方法

1. 实现 Runnable 接口，实现 run 方法，然后 new Thread(Runnable)
2. 继承 Thread 类，重写 run 方法
3. 实现 Callable 接口实现 call 方法，创建 FutureTask 对象 new FutureTask(Callable)，并创建 Thread 对象 new Thread(FutureTask)
4. 通过线程池进行线程的创建和任务提交 execute(Runnable) submit(Runnable) submit(Callable)

### 7.7. ThreadLocal 的实现原理

1. ThreadLocal 可以保证多线程场景下，不同线程各自访问各自的数据不存在数据安全问题
2. ThreadLocal 中核心方法为 get 和 set，必须先 set 再 get
3. 每一个 Thread 对象中，都有一个 threadLocals 的 map, set 方法底层是获取到当前线程对象 Thread 后，给 Thread 对象中的成员属性 threadLocals 赋予一个 ThreadLocalMap 对象，并将当前 ThreadLocal 对象作为此 Map 的 key，设置的值作为 value

```java
public class Thread implements Runnable {
    private volatile String name;
    ThreadLocalMap threadLocals;    // map
    .....
    .....
}

public class ThreadLocal<T> {
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocal.ThreadLocalMap map = this.getMap(t);
        if (map != null) {
            map.set(this, value);   // 把threadlocal加入Thread下的map中
        } else {
            this.createMap(t, value);
        }

    }
}
```

### 7.8. ThreadLocal 如果不 remove 会怎样？

1. 内存泄漏：ThreadLocalMap 的 Entry 为弱引用，当下一次 GC 的时候，弱引用的对象会被回收，会导致 map 的 key 为 null,为 null 的 key 没法从 map 里面 get 出来。导致内存泄漏。
