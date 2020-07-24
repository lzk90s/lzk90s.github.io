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

### stop-the-world
