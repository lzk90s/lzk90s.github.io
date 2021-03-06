<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [1. Atomic 模块](#1-atomic-模块)
  - [1.1. JMM（java memory model）是什么？](#11-jmmjava-memory-model是什么)
  - [1.2. synchronized 作用于静态方法和非静态方法的区别？](#12-synchronized-作用于静态方法和非静态方法的区别)
  - [1.3. synchronized 的实现原理](#13-synchronized-的实现原理)
  - [1.4. volatile 关键字的作用](#14-volatile-关键字的作用)
  - [1.5. volatile 底层是如何实现禁止指令重排的？](#15-volatile-底层是如何实现禁止指令重排的)
  - [1.6. volatile 为什么不能实现原子性？](#16-volatile-为什么不能实现原子性)
  - [1.7. 锁的实现思路](#17-锁的实现思路)
  - [1.8. 乐观锁和悲观锁](#18-乐观锁和悲观锁)
  - [1.9. 乐观锁的实现：CAS（compare and swap）](#19-乐观锁的实现cascompare-and-swap)
  - [1.10. 什么是 CAS 的自旋](#110-什么是-cas-的自旋)
  - [1.11. 什么是 ABA 问题？](#111-什么是-aba-问题)
  - [1.12. ABA 问题的影响](#112-aba-问题的影响)
  - [1.13. 如何解决 ABA 问题？](#113-如何解决-aba-问题)
  - [1.14. java 中的 ABA 方案：AtomicStampedReference](#114-java-中的-aba-方案atomicstampedreference)
  - [1.15. AtomicXXX 的原理](#115-atomicxxx-的原理)
  - [1.16. LongAdder（累加器） 的原理，与 AtomicLong 的区别？](#116-longadder累加器-的原理与-atomiclong-的区别)
- [2. Lock 模块（基于 AQS 实现）](#2-lock-模块基于-aqs-实现)
  - [2.1. AQS（AbstractQueuedSynchronizer）是什么？](#21-aqsabstractqueuedsynchronizer是什么)
  - [2.2. AQS 的原理](#22-aqs-的原理)
  - [2.3. Lock 和 synchronized 的区别？](#23-lock-和-synchronized-的区别)
  - [2.4. 公平锁/非公平锁](#24-公平锁非公平锁)
  - [2.5. synchronized 是公平锁还是非公平锁？](#25-synchronized-是公平锁还是非公平锁)
  - [2.6. synchronized 锁升级过程](#26-synchronized-锁升级过程)
    - [2.6.1. synchronized 中偏向锁的作用？](#261-synchronized-中偏向锁的作用)
    - [2.6.2. synchronized 中轻量级锁（也叫 自旋锁 / CAS）的作用？](#262-synchronized-中轻量级锁也叫-自旋锁-cas的作用)
    - [2.6.3. synchronized 中重量级锁的作用？](#263-synchronized-中重量级锁的作用)
  - [2.7. ReentrantLock 源码实现](#27-reentrantlock-源码实现)
  - [2.8. LockSupport.pack && LockSupport.unpack 的原理](#28-locksupportpack-locksupportunpack-的原理)
  - [2.9. 锁的优化思路](#29-锁的优化思路)
- [3. 同步辅助类模块（基于 AQS 实现）](#3-同步辅助类模块基于-aqs-实现)
  - [3.1. CountDownLatch 的用途](#31-countdownlatch-的用途)
  - [3.2. Semaphore 的用途](#32-semaphore-的用途)
  - [3.3. CyclicBarrier 的用途](#33-cyclicbarrier-的用途)
  - [3.4. CountDownLatch 和 CyclicBarrier 的区别？](#34-countdownlatch-和-cyclicbarrier-的区别)
- [4. 并发集合模块](#4-并发集合模块)
  - [4.1. ConcurrentHashMap](#41-concurrenthashmap)
    - [4.1.1. jdk1.7 ConcurrentHashMap 如何保证线程安全？](#411-jdk17-concurrenthashmap-如何保证线程安全)
    - [4.1.2. jdk1.8 ConcurrentHashMap 如何保证线程安全？](#412-jdk18-concurrenthashmap-如何保证线程安全)
    - [4.1.3. jdk1.8 ConcurrentHashMap 扩容方式](#413-jdk18-concurrenthashmap-扩容方式)
    - [4.1.4. jdk1.8 ConcurrentHashMap 中的几个内部类](#414-jdk18-concurrenthashmap-中的几个内部类)
    - [4.1.5. jdk1.8 ConcurrentHashMap put 流程](#415-jdk18-concurrenthashmap-put-流程)
    - [4.1.6. jdk1.8 ConcurrentHashMap get 流程](#416-jdk18-concurrenthashmap-get-流程)
    - [4.1.7. jdk1.8 ConcurrentHashMap size 流程](#417-jdk18-concurrenthashmap-size-流程)
    - [4.1.8. HashMap, HashTable, ConcurrentHashMap 的区别？](#418-hashmap-hashtable-concurrenthashmap-的区别)
  - [4.2. CopyOnWriteArrayList](#42-copyonwritearraylist)
    - [4.2.1. CopyOnWriteArrayList 的 get 操作](#421-copyonwritearraylist-的-get-操作)
  - [4.3. SkipList](#43-skiplist)
- [5. 线程池模块](#5-线程池模块)
  - [5.1. 线程池 ThreadPoolExecutor](#51-线程池-threadpoolexecutor)
  - [5.2. 线程池满了丢弃策略有哪些？](#52-线程池满了丢弃策略有哪些)
  - [5.3. java 中线程池种类](#53-java-中线程池种类)
  - [5.4. BlockingQueue](#54-blockingqueue)
  - [5.5. java 实现定时器的几种方法](#55-java-实现定时器的几种方法)
  - [5.6. java 创建线程的几种方法](#56-java-创建线程的几种方法)
  - [5.7. ThreadLocal 的实现原理](#57-threadlocal-的实现原理)
  - [5.8. ThreadLocal 如果不 remove 会怎样？](#58-threadlocal-如果不-remove-会怎样)
  - [5.9. ForkJoinPool 实现原理](#59-forkjoinpool-实现原理)
  - [5.10. Future get（timeout) 超时了执行线程的状态？](#510-future-gettimeout-超时了执行线程的状态)
  - [5.11. CompletableFuture 原理](#511-completablefuture-原理)

<!-- /code_chunk_output -->

## 1. Atomic 模块

### 1.1. JMM（java memory model）是什么？

![jmm](jmm.png)

1. 所有的变量都存储在主内存中(虚拟机内存的一部分)，对于所有线程都是共享的。
2. 每条线程都有自己的工作内存，工作内存中保存的是主存中某些变量的拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存中的变量。
3. 线程之间无法直接访问对方的工作内存中的变量，线程间变量的传递均需要通过主内存来完成。

JMM 是一种规范，目的是解决由于多线程通过共享内存进行通信时，存在的本地内存数据不一致、编译器会对代码指令重排序、处理器会对代码乱序执行等带来的问题。目的是保证并发编程场景中的原子性、可见性和有序性。

### 1.2. synchronized 作用于静态方法和非静态方法的区别？

当关键字 Synchronized 加在了静态方法上，由于加静态方法获取的是类锁，那么不论基于哪个类 new 了几个实例，他只有一个 Class 对象，所以只有一把锁，多线程访问会发生互斥。

### 1.3. synchronized 的实现原理

1. 每个对象有一个监视器锁（monitor），当 monitor 被占用时就会处于锁定状态，线程执行 monitorenter 指令时尝试获取 monitor 的所有权，执行完成后会释放 monitor 的所有权
2. 添加了 synchronized 的代码块在汇编中会生成 monitorenter 和 monitorexit 指令，普通方法会生成 **ACC_SYNCHRONIZED** 标志，静态方法会多一个 **ACC_STATIC** 标志
3. 当一条线程执行时遇到 monitorenter 指令时，它会尝试去获得锁，如果获得锁，那么所计数器+1（为什么要加 1，因为它是可重入锁，可根据这个琐计数器判断锁状态），如果没有获得锁，那么阻塞，当它遇到一个 monitoerexit 时，琐计数器会-1，当计数器为 0 时，就释放锁。
4. wait/notify 等方法也依赖于 monitor 对象，这就是为什么只有在同步的块或者方法才能调用 wait/notify 等方法，否则会抛出 java.lang.IllegalMonitorStateException 异常的原因

### 1.4. volatile 关键字的作用

- 保证了不同线程对这个进行操作时的可见性，即一个线程修改了某个变量的值，这新值对其他线程来说是立即可见的。
- 防止指令重排序

> 注意：volatile 无法保证对变量的操作的原子性，所以在多线程场景下，多个线程同时 i++会出现线程不安全的问题，因为 i++不是原子操作

### 1.5. volatile 底层是如何实现禁止指令重排的？

内存屏障

### 1.6. volatile 为什么不能实现原子性？

i++这样的操作，在底层是有多条指令的，volatile 只是保证了变量更新后，用内存屏障让其他线程可见

### 1.7. 锁的实现思路

- 锁是一个共享资源
- 单机多线程情况，内存相同，可以用内存实现锁，例如 CAS，mutex 等
- 多机分布式，可以用一个统一的第三方，来实现共享锁，例如数据库，通过 update 某一行的成功与否，来判断是否获取到锁。

### 1.8. 乐观锁和悲观锁

- 悲观锁具有强烈的排他性，获取到锁后，其他人只能等待锁的释放，例如 synchronized
- 乐观锁并未真正加锁，通常是使用 CAS 方式

### 1.9. 乐观锁的实现：CAS（compare and swap）

CAS 是依赖于 cpu 的原子指令的，linux 下使用了 cmpxchg 原子性操作方法。

### 1.10. 什么是 CAS 的自旋

获取锁失败后，为了避免直接让线程进入阻塞状态而采取的循环一定次数去试着获取锁的行为，为什么能这样做呢，因为用户态和内核态的切换非常消耗系统资源，锁的持有时间一般非常短，所以一般多次尝试就能竞争到锁。

### 1.11. 什么是 ABA 问题？

改了又还原回来，感觉是没有修改过，其实已经变化了。某些情况下是致命的错误，例如链表操作。

### 1.12. ABA 问题的影响

- 操作链表过程中， CAS 问题可能会导致链表错误

### 1.13. 如何解决 ABA 问题？

ABA 问题的根本在于 cas 在修改变量的时候，无法记录变量的状态，比如修改的次数，否修改过这个变量。这样就很容易在一个线程将 A 修改成 B 时，另一个线程又会把 B 修改成 A,造成 casd 多次执行的问题。
所以，解决 ABA 问题的方法就是，引入一个业务无关的变量，来记录对业务变量的修改，通常是版本号，或者时间戳。

### 1.14. java 中的 ABA 方案：AtomicStampedReference

### 1.15. AtomicXXX 的原理

1. 使用 volatile 保存变量，保证多线程变量的可见性，使缓存失效。
2. 用 CAS+自旋方式，实现原子操作

### 1.16. LongAdder（累加器） 的原理，与 AtomicLong 的区别？

LongAdder 类与 AtomicLong 类的区别在于高并发时前者将对单一变量的 CAS 操作分散为对数组 cells 中多个元素的 CAS 操作，取值时进行求和；而在并发较低时仅对 base 变量进行 CAS 操作，与 AtomicLong 类原理相同

## 2. Lock 模块（基于 AQS 实现）

### 2.1. AQS（AbstractQueuedSynchronizer）是什么？

- 抽象同步队列，AQS 定义了一套多线程访问共享资源的同步器框架，ReentrantLock/Semaphore/CountDownLatch 都有使用 AQS
- 核心数据结构 双向链表+state（锁状态） 底层操作 CAS

### 2.2. AQS 的原理

1. AQS 内部使用双向链表，用于保存等待的线程。
2. 当线程竞争锁失败，会把线程构造成一个 Node,加入到双向链表中，同时阻塞该线程。
3. 当获取到锁的线程 unlock 锁后，会从队列中唤醒一个阻塞节点执行。

### 2.3. Lock 和 synchronized 的区别？

- synchronized 是关键字，Lock 是 jdk 中的接口
- synchronized 在发生异常时会自动释放占有的锁，因此不会出现死锁，而 lock 发生异常时候不会主动释放占有的锁必须调用 unlock 方法可能引发死锁。
- lock 可以通过 trylock 来知道有没有获取锁，而 synchronized 不能
- 在 synchronized 中线程 1 获取到锁后线程 2 会一直等待下去，而 lock 可以不用一直等下去。
- synchronized 的锁可重入、不可中断、非公平，而 Lock 锁可重入，可判断，可中断，可公平。
- synchronized 是一个悲观锁，Lock 是一个乐观锁（底层基于 volatile 和 cas 实现）

### 2.4. 公平锁/非公平锁

- 公平锁: 公平锁指多个线程按照申请顺序来依次获取锁
- 非公平锁: 非公平锁指多个线程获取锁的顺序并不是按照顺序来获取的，可以中途抢占

### 2.5. synchronized 是公平锁还是非公平锁？

synchronized 是非公平锁

### 2.6. synchronized 锁升级过程

无锁->偏向锁->自旋锁->重量级锁

偏向锁、自旋锁都是用户空间完成，JVM 自己管理；
重量级锁需要向内核申请

| 锁状态   | 优点                                   | 缺点                                 | 适用场景                           |
| :------- | :------------------------------------- | :----------------------------------- | :--------------------------------- |
| 偏向锁   | 加锁、解锁无额外消耗，和非同步方式近似 | 如果竞争线程多，会有额外锁撤销的消耗 | 基本没有线程竞争的场景             |
| 轻量级锁 | 竞争线程不会阻塞，使用自旋等待         | 如果长时间不能获取锁，会消耗 CPU     | 少量线程竞争，且线程持有锁时间不长 |
| 重量级锁 | 竞争线程被阻塞，减少 CPU 空转          | 线程阻塞，响应时间长                 | 很多线程竞争，锁持有时间长         |

#### 2.6.1. synchronized 中偏向锁的作用？

Vector，StringBuffer 都有很多使用了 syncronized 的同步方法，但是在工业实践中，我们通常是在单线程的时候使用它的，**没有必要 **设计 锁竞争机制。为了在没有竞争的情况下减少锁开销，偏向锁偏向于第一个获得它的线程，把第一个访问的 线程 id（在 C++实现中叫线程指针） 写到 markword 中，而不去真正加锁。如果一直没有被其他线程竞争，则持有偏向锁的线程将不需要进行同步。

#### 2.6.2. synchronized 中轻量级锁（也叫 自旋锁 / CAS）的作用？

偏向锁时，有其他线程来竞争锁，则先把 偏向锁撤销，然后进行 自旋锁（轻量级锁）竞争。

#### 2.6.3. synchronized 中重量级锁的作用？

线程始终得不到锁会自旋消耗 CPU，当竞争很大的时候，锁升级为操作系统级的锁，减少 CPU 自旋消耗。
重量级锁在操作系统层有一个 waitset 等待队列，不需要 CAS 消耗 CPU 时间。由操作系统的 CFS 公平调度策略来调度。

### 2.7. ReentrantLock 源码实现

1. 初始化，可以指定公平锁和非公平锁实现
2. 非公平锁 lock 时，先用 cas 判断下当前锁是否被占用，没有占用直接抢占锁。否则，把当前线程封装成一个 Node,加入到 AQS 的双链表尾部，再用 LockSupport.pack 阻塞当前线程。
3. 非公平锁 unlock 时，释放锁，然后从 AQS 的链表头部取一个 Node,用 LockSupport.unpack 唤醒线程，线程就继续开始竞争锁。

### 2.8. LockSupport.pack && LockSupport.unpack 的原理

pack：阻塞线程，jvm 层，通过 pthread_cond_timedwait 阻塞
unpack：激活线程，jvm 层，通过 pthread_cond_signal 来唤醒阻塞的线程

### 2.9. 锁的优化思路

1. 减小锁的范围：只针对需要加锁的步骤加锁
2. 减小锁的粒度：例如 ConcurrentHashMap 在 1.7 和 1.8 的区别
3. 读写锁分离：将读操作与写操作分别加锁，适用于读操作次数远远大于写操作
4. 无锁方式：ThreadLocal

## 3. 同步辅助类模块（基于 AQS 实现）

### 3.1. CountDownLatch 的用途

- 计数器，某个线程等待 N 个线程执行完毕后再继续执行，N 就是对应的计数，每个执行完毕就减一，直到所有完成。可以看作是 1 个等多个。
- 例如游戏中，需要多个游戏玩家都准备好了，才开始游戏，就可以使用 CountDownLatch。

### 3.2. Semaphore 的用途

- Semaphore 可以定义一个数量用以确定同时有几个任务可以同时访问，Semaphore 通常用于限制可以访问某些资源（物理或逻辑的）的线程数目。
- 例如限制流量大小，最多只能同时有几个人访问。

### 3.3. CyclicBarrier 的用途

- 让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。
- 这个屏障之所以用循环修饰，是因为在所有的线程释放彼此之后，这个屏障是可以重新使用的（reset()方法重置屏障点）。这一点与 CountDownLatch 不同

### 3.4. CountDownLatch 和 CyclicBarrier 的区别？

- 对于 CountDownLatch 来说，重点是“一个线程（多个线程）等待”，而其他的 N 个线程在完成“某件事情”之后，可以终止，也可以等待。而对于 CyclicBarrier，重点是多个线程，在任意一个线程没有完成，所有的线程都必须互相等待，然后继续一起执行。
- CountDownLatch 是计数器，线程完成一个记录一个，只不过计数不是递增而是递减，
- CyclicBarrier 更像是一个阀门，需要所有线程都到达，阀门才能打开，然后继续执行。

## 4. 并发集合模块

| interface | non-thread-safe         | thread-safe                              |
| :-------- | :---------------------- | :--------------------------------------- |
| List      | ArrayList               | CopyOnWriteArrayList                     |
| Map       | HashMap                 | ConcurrentHashMap                        |
| Set       | HashSet / TreeSet       | CopyOnWriteArraySet                      |
| Queue     | ArrayDeque / LinkedList | ArrayBlockingQueue / LinkedBlockingQueue |
| Deque     | ArrayDeque / LinkedList | LinkedBlockingDeque                      |

### 4.1. ConcurrentHashMap

#### 4.1.1. jdk1.7 ConcurrentHashMap 如何保证线程安全？

- 分段锁的思想是把大锁拆分成小锁，把数据分段。
- ConcurrentHashMap 使用 Segment（分段锁）技术，将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问，能够实现真正的并发访问。所以说，ConcurrentHashMap 在并发情况下，不仅保证了线程安全，而且提高了性能。

#### 4.1.2. jdk1.8 ConcurrentHashMap 如何保证线程安全？

- jdk1.8 中，采用了 synchronized+CAS 方式来保证线程安全。锁的粒度更小。
- JDK1.8 的实现降低锁的粒度，JDK1.7 版本锁的粒度是基于 Segment 的，包含多个 HashEntry，而 JDK1.8 锁的粒度就是 HashEntry（首节点）

#### 4.1.3. jdk1.8 ConcurrentHashMap 扩容方式

- 多线程方式扩容，通过给每个线程分配桶区间，避免线程间的争用，每个线程默认处理 16 个桶。
- 通过为每个桶节点加锁，避免 putVal 方法导致数据不一致。
- 同时，在扩容的时候，也会将链表拆成两份，这点和 HashMap 的 resize 方法类似。
- 如果有新的线程想 put 数据时，也会帮助其扩容。

#### 4.1.4. jdk1.8 ConcurrentHashMap 中的几个内部类

- Node：Entry
- TreeNode：红黑树节点
- TreeBin：红黑树
- ForwardingNode：扩容节点

#### 4.1.5. jdk1.8 ConcurrentHashMap put 流程

1. 如果没有初始化就先调用 initTable（）方法来进行初始化过程
2. 如果没有 hash 冲突就直接 CAS 插入
3. 如果还在进行扩容操作就先进行扩容
4. 如果存在 hash 冲突，就加锁来保证线程安全，这里有两种情况，一种是链表形式就直接遍历到尾端插入，一种是红黑树就按照红黑树结构插入，
5. 最后一个如果该链表的数量大于阈值 8，就要先转换成黑红树的结构，break 再一次进入循环(阿里面试官问题，默认的链表大小，超过了这个值就会转换为红黑树);
6. 如果添加成功就调用 addCount（）方法统计 size，并且检查是否需要扩容

#### 4.1.6. jdk1.8 ConcurrentHashMap get 流程

1. 计算 hash 值，定位到该 table 索引位置，如果是首节点符合就返回
2. 如果遇到扩容的时候，会调用标志正在扩容节点 ForwardingNode 的 find 方法，查找该节点，匹配就返回
3. 以上都不符合的话，就往下遍历节点，匹配就返回，否则最后就返回 null

#### 4.1.7. jdk1.8 ConcurrentHashMap size 流程

1. put 的时候，会调用 addCount，在添加的时候计算好了 size
2. size 使用了 LongAdder 的方式，使用 Cell 来减少锁的冲突。

#### 4.1.8. HashMap, HashTable, ConcurrentHashMap 的区别？

- HashMap 是线程不安全的，在多线程环境下，使用 HashMap 进行 put 操作时，可能会引起死循环，导致 CPU 利用率接近 100%，所以在并发情况下不能使用 HashMap。
- 不允许 key 和 value 为 null，HashTable 是线程安全的，不过比较暴力，直接 get/put 等所有相关操作都是 synchronized 的，同步代价大。
- ConcurrentHashMap 采用了“分段锁”机制代替了 HashTable 的“独占锁”，从而大大提高了性能。

### 4.2. CopyOnWriteArrayList

#### 4.2.1. CopyOnWriteArrayList 的 get 操作

### 4.3. SkipList

## 5. 线程池模块

### 5.1. 线程池 ThreadPoolExecutor

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

### 5.2. 线程池满了丢弃策略有哪些？

1. 终止 AbortPolicy
2. 直接丢掉 DiscardPolicy
3. 把最早的丢掉 DiscardOldestPolicy
4. 继续执行 CallerRunsPolicy

### 5.3. java 中线程池种类

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

### 5.4. BlockingQueue

- ArrayBlockingQueue：有界缓存等待队列，数组实现，可以指定缓存队列的大小
- LinkedBlockingQueue：无界缓存等待队列。如果不指定大小，maximumPoolSizes 就相当于无效了。
- SynchronousQueue： 没有容量，是无缓冲等待队列，是一个不存储元素的阻塞队列，会直接将任务交给消费者，必须等队列中的添加元素被消费后才能继续添加新的元素
- DelayedWorkQueue： 延迟队列，ScheduledThreadPoolExecutor 使用。

### 5.5. java 实现定时器的几种方法

- Timer
- ScheduledThreadPool

### 5.6. java 创建线程的几种方法

1. 实现 Runnable 接口，实现 run 方法，然后 new Thread(Runnable)
2. 继承 Thread 类，重写 run 方法
3. 实现 Callable 接口实现 call 方法，创建 FutureTask 对象 new FutureTask(Callable)，并创建 Thread 对象 new Thread(FutureTask)
4. 通过线程池进行线程的创建和任务提交 execute(Runnable) submit(Runnable) submit(Callable)

### 5.7. ThreadLocal 的实现原理

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

### 5.8. ThreadLocal 如果不 remove 会怎样？

1. 内存泄漏：ThreadLocalMap 的 Entry 为弱引用，当下一次 GC 的时候，弱引用的对象会被回收，会导致 map 的 key 为 null,为 null 的 key 没法从 map 里面 get 出来。导致内存泄漏。

### 5.9. ForkJoinPool 实现原理

### 5.10. Future get（timeout) 超时了执行线程的状态？

Future get 超时后，执行线程继续在执行任务，除非手动调用 cancel 取消任务。所以，后续还是可以继续用 get 来获取结果的。

### 5.11. CompletableFuture 原理
