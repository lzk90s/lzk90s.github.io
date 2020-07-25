# jvm

## 类加载器

### 类加载器分类

1. BootstrapClassLoader 启动加载类，主要加载核心类库（JRE 的 lib）
2. ExtClassLoader 扩展类加载器，加载 JRE 的 lib/ext 下的 class 文件和 jar 包
3. AppClassLoader 加载当前应用的 classpath 的所有类
4. BootstrapClassLoader 对 Java 不可见，当获取父加载器为 null 时说明是顶级类加载器

### 类加载机制一:全盘委托

当一个 ClassLoader 装载一个类时，除非显示地使用另一个 ClassLoader，则该类所依赖及引用的类也由这个 CladdLoader 载入。

### 类加载机制二：双亲委派

- 当一个类加载器收到类加载任务，会先交给其父类加载器去完成，因此最终加载任务都会传递到顶层的启动类加载器，只有当父类加载器无法完成加载才会尝试自己去加载。
- 目的是为了避免重复加载，已经加载的无需加载。更加安全，避免了随意加载核心 class。
- 自定义类加载器如果重写了 loadClass 方法会破坏双亲委派模式。

## jvm 内存模型

## GC 算法

### CMS

### G1

### 触发垃圾回收的场景

1. 当 Eden 区和幸存区 From 满时
2. 调用 System.gc()或 Runtime.getRuntime().gc()时，不一定会执行 Full GC
3. 老年代空间不足
4. 方法区空间不足
5. 通过 Minor GC 后进入老年代的平均大小大于老年代的可用内存
6. 幸存区 From 到幸存区 To 时发现 To 空间不足转向老年代且老年代可用内存不够
7. 老年代写满会触发 Full GC 8.持久代被写满会触发 Full GC 9.手动调用 gc 方法会触发 Full GC

### stop-the-world
