# java

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

## 多线程，锁

### 线程池 ThreadPoolExecutor

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

### java 中线程池种类

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

### BlockingQueue

- ArrayBlockingQueue：有界缓存等待队列，数组实现，可以指定缓存队列的大小
- LinkedBlockingQueue：无界缓存等待队列。如果不指定大小，maximumPoolSizes 就相当于无效了。
- SynchronousQueue： 没有容量，是无缓冲等待队列，是一个不存储元素的阻塞队列，会直接将任务交给消费者，必须等队列中的添加元素被消费后才能继续添加新的元素

### java 实现定时器的几种方法

- Timer
- ScheduledThreadPool

### java 创建线程的几种方法

1. 实现 Runnable 接口，实现 run 方法，然后 new Thread(Runnable)
2. 继承 Thread 类，重写 run 方法
3. 实现 Callable 接口实现 call 方法，创建 FutureTask 对象 new FutureTask(Callable)，并创建 Thread 对象 new Thread(FutureTask)
4. 通过线程池进行线程的创建和任务提交 execute(Runnable) submit(Runnable) submit(Callable)

### ThreadLocal

1. ThreadLocal 可以保证多线程场景下，不同线程各自访问各自的数据不存在数据安全问题
2. ThreadLocal 中核心方法为 get 和 set，必须先 set 再 get
3. set 方法底层是获取到当前线程对象 Thread 后，给 Thread 对象中的成员属性 threadLocals 赋予一个 ThreadLocalMap 对象，并将当前 ThreadLocal 对象作为此 Map 的 key，设置的值作为 value

### synchronized 作用于静态方法和非静态方法的区别？

当关键字 Synchronized 加在了静态方法上，由于加静态方法获取的是类锁，那么不论基于哪个类 new 了几个实例，他只有一个 Class 对象，所以只有一把锁，多线程访问会发生互斥。

### synchronized 的原理

### vol

###

### Lock，ReentrantLock

## java 命令常用参数

- -Xss512K 指定 JVM 的栈大小
- Xms 指定 JVM 初始占用的堆内存大小
- -Xmx 指定 JVM 最大堆内存大小

## jdk 中的其他命令
