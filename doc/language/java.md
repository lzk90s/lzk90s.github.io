# java

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [java](#java)
  - [==和 equals 的区别](#和-equals-的区别)
  - [包装类的常量池](#包装类的常量池)
  - [String 常量池](#string-常量池)
  - [自动拆箱装箱](#自动拆箱装箱)
  - [ClassNotFoundException 和 NoClassDefFoundException 的区别？](#classnotfoundexception-和-noclassdeffoundexception-的区别)
  - [sleep 和 wait 的区别？](#sleep-和-wait-的区别)
  - [三种代理模式](#三种代理模式)
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

## sleep 和 wait 的区别？

1. sleep 方法是线程类 Thread 的静态方法，调用该方法会使线程进入睡眠状态，让出执行机会给其他线程，等到时间到了，线程进入就绪状态与其他线程一起竞争 CPU 执行时间
2. sleep 是静态方法，他不能改变对象的锁，当一个 synchronized 块中调用了 sleep，线程虽然进入了休眠，但是对象锁没有被释放，其他线程无法访问这个对象
3. wait 是 Object 方法，当一个线程执行到 wait 方法时，他就进入到一个和该对象相关的池，同时释放锁，是的其他线程能够访问，可以通过 notify，notifyAll 方法来唤醒

## 三种代理模式

### jdk 中的 Proxy

### aspectj
